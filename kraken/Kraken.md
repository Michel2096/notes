

Aquí mostrare la base de datos del proyecto kraken, por la  cua debe tener demasiados tributos, en el contexto de no postergar nada ni dejar al ultimo ninguna cosa 


```
-- ============================================================
--  X Social Network — init.sql completo para Docker
--  Incluye: schema principal + módulo de administradores
--  Uso: montar en /docker-entrypoint-initdb.d/init.sql
-- ============================================================

-- ─────────────────────────────────────────────
--  EXTENSIONS
-- ─────────────────────────────────────────────
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";
CREATE EXTENSION IF NOT EXISTS "btree_gin";
CREATE EXTENSION IF NOT EXISTS "unaccent";

-- ─────────────────────────────────────────────
--  ENUMS
-- ─────────────────────────────────────────────
CREATE TYPE user_status           AS ENUM ('active','suspended','deactivated','pending_verification');
CREATE TYPE post_type             AS ENUM ('post','repost','quote','reply','essay','poll','space_summary');
CREATE TYPE media_type            AS ENUM ('image','gif','video','audio','document');
CREATE TYPE reaction_type         AS ENUM ('like','dislike','love','laugh','sad','angry','celebrate');
CREATE TYPE notif_type            AS ENUM ('like','reply','repost','quote','follow','mention','dm','poll_result','space_invite','system');
CREATE TYPE dm_status             AS ENUM ('sent','delivered','read','deleted');
CREATE TYPE report_reason         AS ENUM ('spam','hate','misinformation','adult_content','violence','doxxing','other');
CREATE TYPE report_status         AS ENUM ('pending','under_review','resolved','dismissed');
CREATE TYPE ad_placement          AS ENUM ('timeline','search','profile','topic','premium');
CREATE TYPE subscription_tier     AS ENUM ('free','basic','premium','premium_plus','organization');
CREATE TYPE space_status          AS ENUM ('scheduled','live','ended');
CREATE TYPE poll_status           AS ENUM ('active','ended');
CREATE TYPE auth_provider         AS ENUM ('local','google','apple','github','twitter_import');
CREATE TYPE badge_type            AS ENUM ('verified','official','government','business','developer','early_adopter','top_writer');
CREATE TYPE list_privacy          AS ENUM ('public','private');
CREATE TYPE admin_role            AS ENUM ('super_admin','admin','moderator','analyst','support','advertiser_admin','content_curator');
CREATE TYPE admin_action_category AS ENUM ('user_management','content_moderation','analytics_access','system_config','advertising','security','billing','trending_topics');

-- ═══════════════════════════════════════════════════════════
--  1. USERS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE users (
    id                UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    username          VARCHAR(50)  NOT NULL UNIQUE,
    display_name      VARCHAR(100) NOT NULL,
    email             VARCHAR(255) NOT NULL UNIQUE,
    phone             VARCHAR(20)  UNIQUE,
    password_hash     TEXT,
    status            user_status       NOT NULL DEFAULT 'pending_verification',
    subscription_tier subscription_tier NOT NULL DEFAULT 'free',
    bio               VARCHAR(300),
    location          VARCHAR(100),
    website           VARCHAR(255),
    birth_date        DATE,
    profile_image_url TEXT,
    banner_image_url  TEXT,
    pinned_post_id    UUID,
    followers_count   INT NOT NULL DEFAULT 0,
    following_count   INT NOT NULL DEFAULT 0,
    posts_count       INT NOT NULL DEFAULT 0,
    likes_given_count INT NOT NULL DEFAULT 0,
    essays_count      INT NOT NULL DEFAULT 0,
    settings JSONB NOT NULL DEFAULT '{
        "is_private": false,
        "allow_dms": "everyone",
        "allow_mentions": "everyone",
        "show_birth_date": false,
        "sensitive_content": false,
        "language": "es",
        "notifications": {
            "email": true,
            "push": true,
            "sms": false,
            "likes": true,
            "reposts": true,
            "new_followers": true,
            "mentions": true,
            "dms": true,
            "product_news": false
        },
        "accessibility": {
            "high_contrast": false,
            "reduce_motion": false,
            "font_size": "medium"
        },
        "theme": "system",
        "timeline_algorithm": "for_you"
    }',
    metadata    JSONB NOT NULL DEFAULT '{}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at  TIMESTAMPTZ
);

CREATE INDEX idx_users_username      ON users (username);
CREATE INDEX idx_users_email         ON users (email);
CREATE INDEX idx_users_status        ON users (status);
CREATE INDEX idx_users_sub_tier      ON users (subscription_tier);
CREATE INDEX idx_users_settings_gin  ON users USING gin (settings);
CREATE INDEX idx_users_metadata_gin  ON users USING gin (metadata);
CREATE INDEX idx_users_created       ON users USING brin (created_at);
CREATE INDEX idx_users_display_trgm  ON users USING gin (display_name gin_trgm_ops);
CREATE INDEX idx_users_username_trgm ON users USING gin (username gin_trgm_ops);

-- ═══════════════════════════════════════════════════════════
--  2. BADGES
-- ═══════════════════════════════════════════════════════════
CREATE TABLE user_badges (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id    UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    badge      badge_type NOT NULL,
    granted_by UUID REFERENCES users(id),
    granted_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    expires_at TIMESTAMPTZ,
    metadata   JSONB NOT NULL DEFAULT '{}',
    UNIQUE (user_id, badge)
);
CREATE INDEX idx_badges_user ON user_badges (user_id);

-- ═══════════════════════════════════════════════════════════
--  3. AUTENTICACIÓN
-- ═══════════════════════════════════════════════════════════
CREATE TABLE auth_sessions (
    id             UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id        UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    token_hash     TEXT NOT NULL UNIQUE,
    provider       auth_provider NOT NULL DEFAULT 'local',
    device_info    JSONB NOT NULL DEFAULT '{}',
    is_active      BOOLEAN NOT NULL DEFAULT TRUE,
    created_at     TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    last_active_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    expires_at     TIMESTAMPTZ NOT NULL DEFAULT (NOW() + INTERVAL '30 days')
);
CREATE INDEX idx_sessions_user   ON auth_sessions (user_id);
CREATE INDEX idx_sessions_active ON auth_sessions (is_active, expires_at);

CREATE TABLE oauth_accounts (
    id            UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id       UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    provider      auth_provider NOT NULL,
    provider_uid  TEXT NOT NULL,
    access_token  TEXT,
    refresh_token TEXT,
    token_expires TIMESTAMPTZ,
    raw_profile   JSONB NOT NULL DEFAULT '{}',
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE (provider, provider_uid)
);

CREATE TABLE auth_events (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id    UUID REFERENCES users(id) ON DELETE SET NULL,
    event_type VARCHAR(50) NOT NULL,
    ip_address INET,
    metadata   JSONB NOT NULL DEFAULT '{}',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_auth_events_user ON auth_events (user_id, created_at DESC);

CREATE TABLE user_2fa (
    id           UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id      UUID NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    method       VARCHAR(20) NOT NULL DEFAULT 'totp',
    secret_enc   TEXT,
    backup_codes JSONB,
    is_enabled   BOOLEAN NOT NULL DEFAULT FALSE,
    last_used_at TIMESTAMPTZ,
    created_at   TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE password_resets (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id    UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    token_hash TEXT NOT NULL UNIQUE,
    used_at    TIMESTAMPTZ,
    expires_at TIMESTAMPTZ NOT NULL DEFAULT (NOW() + INTERVAL '1 hour'),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- ═══════════════════════════════════════════════════════════
--  4. SUBSCRIPCIONES
-- ═══════════════════════════════════════════════════════════
CREATE TABLE subscriptions (
    id                   UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id              UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    tier                 subscription_tier NOT NULL,
    status               VARCHAR(20) NOT NULL DEFAULT 'active',
    billing_cycle        VARCHAR(10) NOT NULL DEFAULT 'monthly',
    price_cents          INT NOT NULL,
    currency             CHAR(3) NOT NULL DEFAULT 'USD',
    stripe_sub_id        TEXT UNIQUE,
    current_period_start TIMESTAMPTZ,
    current_period_end   TIMESTAMPTZ,
    cancelled_at         TIMESTAMPTZ,
    metadata             JSONB NOT NULL DEFAULT '{}',
    created_at           TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at           TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_subs_user   ON subscriptions (user_id);
CREATE INDEX idx_subs_status ON subscriptions (status);

-- ═══════════════════════════════════════════════════════════
--  5. MEDIA
-- ═══════════════════════════════════════════════════════════
CREATE TABLE media (
    id                UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    uploader_id       UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    media_type        media_type NOT NULL,
    url               TEXT NOT NULL,
    cdn_key           TEXT,
    thumbnail_url     TEXT,
    alt_text          TEXT,
    file_size_bytes   BIGINT,
    duration_secs     NUMERIC(10,2),
    width_px          INT,
    height_px         INT,
    is_sensitive      BOOLEAN NOT NULL DEFAULT FALSE,
    processing_status VARCHAR(20) NOT NULL DEFAULT 'pending',
    metadata          JSONB NOT NULL DEFAULT '{}',
    created_at        TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_media_uploader ON media (uploader_id);
CREATE INDEX idx_media_type     ON media (media_type);
CREATE INDEX idx_media_meta_gin ON media USING gin (metadata);

-- ═══════════════════════════════════════════════════════════
--  6. HASHTAGS & TOPICS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE hashtags (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tag         VARCHAR(280) NOT NULL UNIQUE,
    posts_count INT NOT NULL DEFAULT 0,
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_hashtags_count ON hashtags (posts_count DESC);
CREATE INDEX idx_hashtags_trgm  ON hashtags USING gin (tag gin_trgm_ops);

CREATE TABLE topics (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name        VARCHAR(100) NOT NULL UNIQUE,
    slug        VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    icon_url    TEXT,
    parent_id   UUID REFERENCES topics(id),
    metadata    JSONB NOT NULL DEFAULT '{}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_topics_parent ON topics (parent_id);
CREATE INDEX idx_topics_slug   ON topics (slug);

CREATE TABLE user_topic_follows (
    user_id     UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    topic_id    UUID NOT NULL REFERENCES topics(id) ON DELETE CASCADE,
    score       NUMERIC(5,2) NOT NULL DEFAULT 1.0,
    followed_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (user_id, topic_id)
);

-- ═══════════════════════════════════════════════════════════
--  7. POSTS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE posts (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    author_id       UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    post_type       post_type NOT NULL DEFAULT 'post',
    content         TEXT,
    content_html    TEXT,
    content_tsvec   TSVECTOR,
    lang            CHAR(5) DEFAULT 'es',
    parent_id       UUID REFERENCES posts(id) ON DELETE SET NULL,
    root_id         UUID REFERENCES posts(id) ON DELETE SET NULL,
    repost_of_id    UUID REFERENCES posts(id) ON DELETE SET NULL,
    essay_title     VARCHAR(280),
    essay_subtitle  VARCHAR(500),
    essay_cover_url TEXT,
    read_time_mins  SMALLINT,
    is_sensitive    BOOLEAN NOT NULL DEFAULT FALSE,
    is_pinned       BOOLEAN NOT NULL DEFAULT FALSE,
    audience        VARCHAR(20) NOT NULL DEFAULT 'public',
    geo             JSONB,
    link_preview    JSONB,
    likes_count     INT NOT NULL DEFAULT 0,
    reposts_count   INT NOT NULL DEFAULT 0,
    quotes_count    INT NOT NULL DEFAULT 0,
    replies_count   INT NOT NULL DEFAULT 0,
    bookmarks_count INT NOT NULL DEFAULT 0,
    views_count     BIGINT NOT NULL DEFAULT 0,
    scheduled_at    TIMESTAMPTZ,
    published_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    edit_count      SMALLINT NOT NULL DEFAULT 0,
    last_edited_at  TIMESTAMPTZ,
    metadata        JSONB NOT NULL DEFAULT '{}',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at      TIMESTAMPTZ
);
CREATE INDEX idx_posts_author       ON posts (author_id, published_at DESC);
CREATE INDEX idx_posts_parent       ON posts (parent_id);
CREATE INDEX idx_posts_root         ON posts (root_id);
CREATE INDEX idx_posts_repost_of    ON posts (repost_of_id);
CREATE INDEX idx_posts_type         ON posts (post_type);
CREATE INDEX idx_posts_published    ON posts (published_at DESC);
CREATE INDEX idx_posts_scheduled    ON posts (scheduled_at) WHERE scheduled_at IS NOT NULL;
CREATE INDEX idx_posts_tsvec        ON posts USING gin (content_tsvec);
CREATE INDEX idx_posts_metadata_gin ON posts USING gin (metadata);
CREATE INDEX idx_posts_geo_gin      ON posts USING gin (geo);

CREATE TABLE post_edits (
    id           UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    post_id      UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    prev_content TEXT,
    prev_html    TEXT,
    edited_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    edit_reason  TEXT,
    metadata     JSONB NOT NULL DEFAULT '{}'
);
CREATE INDEX idx_post_edits_post ON post_edits (post_id, edited_at DESC);

CREATE TABLE post_media (
    post_id  UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    media_id UUID NOT NULL REFERENCES media(id) ON DELETE CASCADE,
    position SMALLINT NOT NULL DEFAULT 0,
    PRIMARY KEY (post_id, media_id)
);

CREATE TABLE post_hashtags (
    post_id    UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    hashtag_id UUID NOT NULL REFERENCES hashtags(id) ON DELETE CASCADE,
    PRIMARY KEY (post_id, hashtag_id)
);
CREATE INDEX idx_post_hashtags_tag ON post_hashtags (hashtag_id);

CREATE TABLE post_topics (
    post_id  UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    topic_id UUID NOT NULL REFERENCES topics(id) ON DELETE CASCADE,
    PRIMARY KEY (post_id, topic_id)
);

CREATE TABLE post_mentions (
    post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    PRIMARY KEY (post_id, user_id)
);
CREATE INDEX idx_mentions_user ON post_mentions (user_id);

-- ═══════════════════════════════════════════════════════════
--  8. POLLS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE polls (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    post_id     UUID NOT NULL UNIQUE REFERENCES posts(id) ON DELETE CASCADE,
    status      poll_status NOT NULL DEFAULT 'active',
    ends_at     TIMESTAMPTZ NOT NULL,
    total_votes INT NOT NULL DEFAULT 0,
    settings    JSONB NOT NULL DEFAULT '{"allow_multiple_votes": false}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE poll_options (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    poll_id     UUID NOT NULL REFERENCES polls(id) ON DELETE CASCADE,
    label       VARCHAR(280) NOT NULL,
    votes_count INT NOT NULL DEFAULT 0,
    position    SMALLINT NOT NULL DEFAULT 0
);
CREATE INDEX idx_poll_options_poll ON poll_options (poll_id, position);

CREATE TABLE poll_votes (
    id             UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    poll_id        UUID NOT NULL REFERENCES polls(id) ON DELETE CASCADE,
    poll_option_id UUID NOT NULL REFERENCES poll_options(id) ON DELETE CASCADE,
    user_id        UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    voted_at       TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE (poll_id, user_id, poll_option_id)
);
CREATE INDEX idx_poll_votes_user ON poll_votes (user_id);

-- ═══════════════════════════════════════════════════════════
--  9. REACTIONS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE reactions (
    id            UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id       UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    post_id       UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    reaction_type reaction_type NOT NULL DEFAULT 'like',
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE (user_id, post_id, reaction_type)
);
CREATE INDEX idx_reactions_post ON reactions (post_id, reaction_type);
CREATE INDEX idx_reactions_user ON reactions (user_id, created_at DESC);

-- ═══════════════════════════════════════════════════════════
--  10. BOOKMARKS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE bookmark_folders (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id    UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name       VARCHAR(100) NOT NULL,
    emoji      VARCHAR(10),
    is_default BOOLEAN NOT NULL DEFAULT FALSE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_bkf_user ON bookmark_folders (user_id);

CREATE TABLE bookmarks (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id    UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    post_id    UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    folder_id  UUID REFERENCES bookmark_folders(id) ON DELETE SET NULL,
    note       TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE (user_id, post_id)
);
CREATE INDEX idx_bookmarks_user   ON bookmarks (user_id, created_at DESC);
CREATE INDEX idx_bookmarks_folder ON bookmarks (folder_id);

-- ═══════════════════════════════════════════════════════════
--  11. FOLLOWS / MUTES / BLOCKS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE follows (
    follower_id    UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    following_id   UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    is_accepted    BOOLEAN NOT NULL DEFAULT TRUE,
    notify_on_post BOOLEAN NOT NULL DEFAULT FALSE,
    created_at     TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (follower_id, following_id),
    CHECK (follower_id <> following_id)
);
CREATE INDEX idx_follows_following ON follows (following_id, created_at DESC);
CREATE INDEX idx_follows_follower  ON follows (follower_id,  created_at DESC);

CREATE TABLE mutes (
    muter_id   UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    muted_id   UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    options    JSONB NOT NULL DEFAULT '{"mute_notifications": true, "mute_timeline": true}',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    expires_at TIMESTAMPTZ,
    PRIMARY KEY (muter_id, muted_id)
);

CREATE TABLE blocks (
    blocker_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    blocked_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    reason     TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (blocker_id, blocked_id)
);

-- ═══════════════════════════════════════════════════════════
--  12. DIRECT MESSAGES
-- ═══════════════════════════════════════════════════════════
CREATE TABLE dm_conversations (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    created_by  UUID NOT NULL REFERENCES users(id),
    is_group    BOOLEAN NOT NULL DEFAULT FALSE,
    group_name  VARCHAR(100),
    group_image TEXT,
    last_msg_at TIMESTAMPTZ,
    metadata    JSONB NOT NULL DEFAULT '{}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE dm_participants (
    conversation_id UUID NOT NULL REFERENCES dm_conversations(id) ON DELETE CASCADE,
    user_id         UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    role            VARCHAR(20) NOT NULL DEFAULT 'member',
    joined_at       TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    last_read_at    TIMESTAMPTZ,
    is_muted        BOOLEAN NOT NULL DEFAULT FALSE,
    PRIMARY KEY (conversation_id, user_id)
);
CREATE INDEX idx_dm_parts_user ON dm_participants (user_id);

CREATE TABLE dm_messages (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    conversation_id UUID NOT NULL REFERENCES dm_conversations(id) ON DELETE CASCADE,
    sender_id       UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    status          dm_status NOT NULL DEFAULT 'sent',
    content         TEXT,
    reply_to_id     UUID REFERENCES dm_messages(id) ON DELETE SET NULL,
    is_deleted      BOOLEAN NOT NULL DEFAULT FALSE,
    reactions       JSONB NOT NULL DEFAULT '{}',
    attachments     JSONB NOT NULL DEFAULT '[]',
    metadata        JSONB NOT NULL DEFAULT '{}',
    sent_at         TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_dm_msg_conv   ON dm_messages (conversation_id, sent_at DESC);
CREATE INDEX idx_dm_msg_sender ON dm_messages (sender_id);

-- ═══════════════════════════════════════════════════════════
--  13. NOTIFICATIONS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE notifications (
    id           UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    recipient_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    actor_id     UUID REFERENCES users(id) ON DELETE SET NULL,
    notif_type   notif_type NOT NULL,
    entity_type  VARCHAR(30),
    entity_id    UUID,
    is_read      BOOLEAN NOT NULL DEFAULT FALSE,
    is_seen      BOOLEAN NOT NULL DEFAULT FALSE,
    grouped_count INT NOT NULL DEFAULT 1,
    payload      JSONB NOT NULL DEFAULT '{}',
    created_at   TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_notif_recipient ON notifications (recipient_id, is_read, created_at DESC);
CREATE INDEX idx_notif_entity    ON notifications (entity_type, entity_id);

-- ═══════════════════════════════════════════════════════════
--  14. SPACES
-- ═══════════════════════════════════════════════════════════
CREATE TABLE spaces (
    id             UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    host_id        UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    title          VARCHAR(280),
    status         space_status NOT NULL DEFAULT 'scheduled',
    scheduled_at   TIMESTAMPTZ,
    started_at     TIMESTAMPTZ,
    ended_at       TIMESTAMPTZ,
    recording_url  TEXT,
    listeners_peak INT NOT NULL DEFAULT 0,
    topics         UUID[],
    metadata       JSONB NOT NULL DEFAULT '{}',
    created_at     TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_spaces_host   ON spaces (host_id);
CREATE INDEX idx_spaces_status ON spaces (status);

CREATE TABLE space_participants (
    id        UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    space_id  UUID NOT NULL REFERENCES spaces(id) ON DELETE CASCADE,
    user_id   UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    role      VARCHAR(20) NOT NULL DEFAULT 'listener',
    joined_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    left_at   TIMESTAMPTZ,
    is_muted  BOOLEAN NOT NULL DEFAULT TRUE,
    metadata  JSONB NOT NULL DEFAULT '{}'
);
CREATE INDEX idx_space_parts ON space_participants (space_id);

-- ═══════════════════════════════════════════════════════════
--  15. LISTS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE lists (
    id                UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    owner_id          UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name              VARCHAR(100) NOT NULL,
    description       TEXT,
    privacy           list_privacy NOT NULL DEFAULT 'public',
    cover_url         TEXT,
    members_count     INT NOT NULL DEFAULT 0,
    subscribers_count INT NOT NULL DEFAULT 0,
    metadata          JSONB NOT NULL DEFAULT '{}',
    created_at        TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_lists_owner ON lists (owner_id);

CREATE TABLE list_members (
    list_id  UUID NOT NULL REFERENCES lists(id) ON DELETE CASCADE,
    user_id  UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    added_by UUID REFERENCES users(id),
    added_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (list_id, user_id)
);

CREATE TABLE list_subscribers (
    list_id       UUID NOT NULL REFERENCES lists(id) ON DELETE CASCADE,
    subscriber_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    subscribed_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (list_id, subscriber_id)
);

-- ═══════════════════════════════════════════════════════════
--  16. COMMUNITIES
-- ═══════════════════════════════════════════════════════════
CREATE TABLE communities (
    id            UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name          VARCHAR(100) NOT NULL,
    slug          VARCHAR(100) NOT NULL UNIQUE,
    description   TEXT,
    cover_url     TEXT,
    icon_url      TEXT,
    is_private    BOOLEAN NOT NULL DEFAULT FALSE,
    members_count INT NOT NULL DEFAULT 0,
    rules         JSONB NOT NULL DEFAULT '[]',
    metadata      JSONB NOT NULL DEFAULT '{}',
    created_by    UUID NOT NULL REFERENCES users(id),
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE community_members (
    community_id UUID NOT NULL REFERENCES communities(id) ON DELETE CASCADE,
    user_id      UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    role         VARCHAR(20) NOT NULL DEFAULT 'member',
    joined_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (community_id, user_id)
);

-- ═══════════════════════════════════════════════════════════
--  17. TRENDING & SEARCH
-- ═══════════════════════════════════════════════════════════
CREATE TABLE trending_snapshots (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    region     CHAR(2) NOT NULL DEFAULT 'MX',
    snapped_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    data       JSONB NOT NULL DEFAULT '[]'
);
CREATE INDEX idx_trending_region ON trending_snapshots (region, snapped_at DESC);

CREATE TABLE search_queries (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id    UUID REFERENCES users(id) ON DELETE SET NULL,
    query      TEXT NOT NULL,
    results    JSONB NOT NULL DEFAULT '{}',
    session_id TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_search_user       ON search_queries (user_id, created_at DESC);
CREATE INDEX idx_search_query_trgm ON search_queries USING gin (query gin_trgm_ops);

-- ═══════════════════════════════════════════════════════════
--  18. ANALYTICS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE post_impressions (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    post_id     UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    viewer_id   UUID REFERENCES users(id) ON DELETE SET NULL,
    source      VARCHAR(30) NOT NULL DEFAULT 'timeline',
    device      JSONB NOT NULL DEFAULT '{}',
    duration_ms INT,
    metadata    JSONB NOT NULL DEFAULT '{}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_impressions_post   ON post_impressions (post_id);
CREATE INDEX idx_impressions_viewer ON post_impressions (viewer_id);
CREATE INDEX idx_impressions_date   ON post_impressions USING brin (created_at);

CREATE TABLE post_engagement_summary (
    post_id         UUID PRIMARY KEY REFERENCES posts(id) ON DELETE CASCADE,
    impressions     BIGINT NOT NULL DEFAULT 0,
    profile_clicks  INT NOT NULL DEFAULT 0,
    link_clicks     INT NOT NULL DEFAULT 0,
    detail_expands  INT NOT NULL DEFAULT 0,
    video_plays     INT NOT NULL DEFAULT 0,
    avg_duration_ms INT,
    by_source       JSONB NOT NULL DEFAULT '{}',
    by_device       JSONB NOT NULL DEFAULT '{}',
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE user_analytics_daily (
    id               UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id          UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    date             DATE NOT NULL,
    impressions      INT NOT NULL DEFAULT 0,
    profile_visits   INT NOT NULL DEFAULT 0,
    new_followers    INT NOT NULL DEFAULT 0,
    posts_published  INT NOT NULL DEFAULT 0,
    likes_received   INT NOT NULL DEFAULT 0,
    reposts_received INT NOT NULL DEFAULT 0,
    top_post_id      UUID REFERENCES posts(id),
    breakdown        JSONB NOT NULL DEFAULT '{}',
    UNIQUE (user_id, date)
);
CREATE INDEX idx_user_analytics_date ON user_analytics_daily (user_id, date DESC);

-- ═══════════════════════════════════════════════════════════
--  19. REPORTS / MODERATION
-- ═══════════════════════════════════════════════════════════
CREATE TABLE reports (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    reporter_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    entity_type VARCHAR(20) NOT NULL,
    entity_id   UUID NOT NULL,
    reason      report_reason NOT NULL,
    description TEXT,
    status      report_status NOT NULL DEFAULT 'pending',
    reviewed_by UUID REFERENCES users(id),
    reviewed_at TIMESTAMPTZ,
    resolution  TEXT,
    metadata    JSONB NOT NULL DEFAULT '{}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_reports_status   ON reports (status, created_at);
CREATE INDEX idx_reports_entity   ON reports (entity_type, entity_id);
CREATE INDEX idx_reports_reporter ON reports (reporter_id);

CREATE TABLE moderation_actions (
    id           UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    moderator_id UUID NOT NULL REFERENCES users(id),
    target_type  VARCHAR(20) NOT NULL,
    target_id    UUID NOT NULL,
    action       VARCHAR(50) NOT NULL,
    reason       TEXT,
    report_id    UUID REFERENCES reports(id),
    metadata     JSONB NOT NULL DEFAULT '{}',
    expires_at   TIMESTAMPTZ,
    created_at   TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_mod_target ON moderation_actions (target_type, target_id);

CREATE TABLE content_labels (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    post_id    UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    label_type VARCHAR(50) NOT NULL,
    source     VARCHAR(30) NOT NULL DEFAULT 'automated',
    confidence NUMERIC(4,3),
    metadata   JSONB NOT NULL DEFAULT '{}',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE community_notes (
    id                UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    post_id           UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    author_id         UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    note_text         TEXT NOT NULL,
    is_visible        BOOLEAN NOT NULL DEFAULT FALSE,
    helpful_count     INT NOT NULL DEFAULT 0,
    not_helpful_count INT NOT NULL DEFAULT 0,
    status            VARCHAR(20) NOT NULL DEFAULT 'pending',
    created_at        TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE community_note_ratings (
    note_id    UUID NOT NULL REFERENCES community_notes(id) ON DELETE CASCADE,
    rater_id   UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    is_helpful BOOLEAN NOT NULL,
    tags       JSONB NOT NULL DEFAULT '[]',
    rated_at   TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (note_id, rater_id)
);

-- ═══════════════════════════════════════════════════════════
--  20. ADVERTISING
-- ═══════════════════════════════════════════════════════════
CREATE TABLE advertisers (
    id            UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id       UUID NOT NULL UNIQUE REFERENCES users(id),
    company_name  VARCHAR(200) NOT NULL,
    billing_email VARCHAR(255) NOT NULL,
    status        VARCHAR(20) NOT NULL DEFAULT 'pending',
    credit_cents  BIGINT NOT NULL DEFAULT 0,
    metadata      JSONB NOT NULL DEFAULT '{}',
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE ad_campaigns (
    id            UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    advertiser_id UUID NOT NULL REFERENCES advertisers(id) ON DELETE CASCADE,
    name          VARCHAR(200) NOT NULL,
    objective     VARCHAR(50) NOT NULL,
    status        VARCHAR(20) NOT NULL DEFAULT 'draft',
    budget_cents  BIGINT NOT NULL,
    spent_cents   BIGINT NOT NULL DEFAULT 0,
    starts_at     TIMESTAMPTZ,
    ends_at       TIMESTAMPTZ,
    targeting     JSONB NOT NULL DEFAULT '{}',
    metadata      JSONB NOT NULL DEFAULT '{}',
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at    TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_campaigns_advertiser ON ad_campaigns (advertiser_id);
CREATE INDEX idx_campaigns_status     ON ad_campaigns (status, starts_at, ends_at);

CREATE TABLE ad_creatives (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    campaign_id UUID NOT NULL REFERENCES ad_campaigns(id) ON DELETE CASCADE,
    placement   ad_placement NOT NULL DEFAULT 'timeline',
    post_id     UUID REFERENCES posts(id),
    headline    VARCHAR(100),
    cta_label   VARCHAR(30),
    cta_url     TEXT,
    media_ids   UUID[],
    status      VARCHAR(20) NOT NULL DEFAULT 'pending_review',
    metadata    JSONB NOT NULL DEFAULT '{}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE ad_impressions (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    creative_id UUID NOT NULL REFERENCES ad_creatives(id) ON DELETE CASCADE,
    viewer_id   UUID REFERENCES users(id) ON DELETE SET NULL,
    placement   ad_placement NOT NULL,
    was_clicked BOOLEAN NOT NULL DEFAULT FALSE,
    cost_cents  INT NOT NULL DEFAULT 0,
    device      JSONB NOT NULL DEFAULT '{}',
    metadata    JSONB NOT NULL DEFAULT '{}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_ad_imp_creative ON ad_impressions (creative_id);
CREATE INDEX idx_ad_imp_date     ON ad_impressions USING brin (created_at);

-- ═══════════════════════════════════════════════════════════
--  21. ESSAYS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE essay_stats (
    post_id           UUID PRIMARY KEY REFERENCES posts(id) ON DELETE CASCADE,
    total_reads       INT NOT NULL DEFAULT 0,
    avg_read_pct      NUMERIC(5,2) NOT NULL DEFAULT 0,
    subscribers_reads INT NOT NULL DEFAULT 0,
    external_reads    INT NOT NULL DEFAULT 0,
    top_referrers     JSONB NOT NULL DEFAULT '[]',
    updated_at        TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE essay_subscriptions (
    subscriber_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    author_id     UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    tier          VARCHAR(30) NOT NULL DEFAULT 'free',
    price_cents   INT NOT NULL DEFAULT 0,
    stripe_sub_id TEXT,
    status        VARCHAR(20) NOT NULL DEFAULT 'active',
    metadata      JSONB NOT NULL DEFAULT '{}',
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (subscriber_id, author_id)
);

-- ═══════════════════════════════════════════════════════════
--  22. ALGORITMO / FEED
-- ═══════════════════════════════════════════════════════════
CREATE TABLE user_interaction_scores (
    user_id    UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    target_id  UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    score      NUMERIC(8,4) NOT NULL DEFAULT 0,
    breakdown  JSONB NOT NULL DEFAULT '{}',
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    PRIMARY KEY (user_id, target_id)
);

CREATE TABLE feed_cache (
    user_id      UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    feed_type    VARCHAR(20) NOT NULL DEFAULT 'for_you',
    post_ids     UUID[] NOT NULL,
    scores       JSONB NOT NULL DEFAULT '[]',
    generated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    expires_at   TIMESTAMPTZ NOT NULL DEFAULT (NOW() + INTERVAL '5 minutes'),
    PRIMARY KEY (user_id, feed_type)
);

-- ═══════════════════════════════════════════════════════════
--  23. PUSH TOKENS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE push_tokens (
    id           UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id      UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    token        TEXT NOT NULL UNIQUE,
    platform     VARCHAR(10) NOT NULL,
    is_active    BOOLEAN NOT NULL DEFAULT TRUE,
    device_info  JSONB NOT NULL DEFAULT '{}',
    created_at   TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    last_used_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_push_tokens_user ON push_tokens (user_id, is_active);

-- ═══════════════════════════════════════════════════════════
--  24. LINK CLICKS
-- ═══════════════════════════════════════════════════════════
CREATE TABLE link_clicks (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    post_id    UUID REFERENCES posts(id) ON DELETE SET NULL,
    user_id    UUID REFERENCES users(id) ON DELETE SET NULL,
    url        TEXT NOT NULL,
    short_code VARCHAR(20),
    ip_hash    TEXT,
    referrer   TEXT,
    device     JSONB NOT NULL DEFAULT '{}',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_link_clicks_post ON link_clicks (post_id);
CREATE INDEX idx_link_clicks_date ON link_clicks USING brin (created_at);

-- ═══════════════════════════════════════════════════════════
--  25. FK DIFERIDA: users.pinned_post_id
-- ═══════════════════════════════════════════════════════════
ALTER TABLE users ADD CONSTRAINT fk_pinned_post
    FOREIGN KEY (pinned_post_id) REFERENCES posts(id) ON DELETE SET NULL;

-- ═══════════════════════════════════════════════════════════
--  MÓDULO ADMINISTRADORES
-- ═══════════════════════════════════════════════════════════

-- ─────────────────────────────────────────────
--  A1. ADMINS
-- ─────────────────────────────────────────────
CREATE TABLE admins (
    id            UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id       UUID NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    role          admin_role NOT NULL,
    is_active     BOOLEAN NOT NULL DEFAULT TRUE,
    granted_by    UUID NOT NULL REFERENCES users(id),
    revoked_by    UUID REFERENCES users(id),
    revoked_at    TIMESTAMPTZ,
    revoke_reason TEXT,
    access_config JSONB NOT NULL DEFAULT '{}',
    notes         TEXT,
    granted_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at    TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_admins_user   ON admins (user_id);
CREATE INDEX idx_admins_role   ON admins (role);
CREATE INDEX idx_admins_active ON admins (is_active);

-- ─────────────────────────────────────────────
--  A2. SESIONES DE ADMIN  (se crea ANTES del audit log)
-- ─────────────────────────────────────────────
CREATE TABLE admin_sessions (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    admin_id        UUID NOT NULL REFERENCES admins(id) ON DELETE CASCADE,
    token_hash      TEXT NOT NULL UNIQUE,
    ip_address      INET,
    user_agent      TEXT,
    is_active       BOOLEAN NOT NULL DEFAULT TRUE,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    last_active_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    expires_at      TIMESTAMPTZ NOT NULL DEFAULT (NOW() + INTERVAL '8 hours'),
    mfa_verified    BOOLEAN NOT NULL DEFAULT FALSE,
    mfa_verified_at TIMESTAMPTZ,
    metadata        JSONB NOT NULL DEFAULT '{}',
    terminated_at   TIMESTAMPTZ,
    terminated_by   UUID REFERENCES admins(id)
);
CREATE INDEX idx_admin_sessions_admin  ON admin_sessions (admin_id);
CREATE INDEX idx_admin_sessions_active ON admin_sessions (is_active, expires_at);

-- ─────────────────────────────────────────────
--  A3. AUDIT LOG  (referencia admin_sessions, por eso va después)
-- ─────────────────────────────────────────────
CREATE TABLE admin_audit_log (
    id               UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    admin_id         UUID NOT NULL REFERENCES admins(id) ON DELETE SET NULL,
    admin_user_id    UUID NOT NULL REFERENCES users(id),
    category         admin_action_category NOT NULL,
    action           VARCHAR(100) NOT NULL,
    description      TEXT,
    target_type      VARCHAR(30),
    target_id        UUID,
    target_snapshot  JSONB,
    success          BOOLEAN NOT NULL DEFAULT TRUE,
    error_message    TEXT,
    ip_address       INET,
    user_agent       TEXT,
    admin_session_id UUID REFERENCES admin_sessions(id) ON DELETE SET NULL,
    metadata         JSONB NOT NULL DEFAULT '{}',
    created_at       TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_audit_admin   ON admin_audit_log (admin_id, created_at DESC);
CREATE INDEX idx_audit_target  ON admin_audit_log (target_type, target_id);
CREATE INDEX idx_audit_action  ON admin_audit_log (category, action);
CREATE INDEX idx_audit_date    ON admin_audit_log USING brin (created_at);

-- ─────────────────────────────────────────────
--  A4. PERMISOS GRANULARES
-- ─────────────────────────────────────────────
CREATE TABLE admin_permissions (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    admin_id   UUID NOT NULL REFERENCES admins(id) ON DELETE CASCADE,
    category   admin_action_category NOT NULL,
    action     VARCHAR(100) NOT NULL,
    is_granted BOOLEAN NOT NULL DEFAULT TRUE,
    granted_by UUID REFERENCES admins(id),
    metadata   JSONB NOT NULL DEFAULT '{}',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE (admin_id, category, action)
);
CREATE INDEX idx_admin_perms_admin    ON admin_permissions (admin_id);
CREATE INDEX idx_admin_perms_category ON admin_permissions (category, action);

-- ─────────────────────────────────────────────
--  A5. PERMISOS POR DEFECTO POR ROL
-- ─────────────────────────────────────────────
CREATE TABLE admin_role_defaults (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    role       admin_role NOT NULL,
    category   admin_action_category NOT NULL,
    action     VARCHAR(100) NOT NULL,
    is_granted BOOLEAN NOT NULL DEFAULT TRUE,
    UNIQUE (role, category, action)
);

INSERT INTO admin_role_defaults (role, category, action) VALUES
    -- super_admin
    ('super_admin','user_management','view_users'),
    ('super_admin','user_management','edit_user'),
    ('super_admin','user_management','suspend_user'),
    ('super_admin','user_management','ban_user'),
    ('super_admin','user_management','delete_user'),
    ('super_admin','user_management','reset_password'),
    ('super_admin','user_management','impersonate'),
    ('super_admin','content_moderation','view_reports'),
    ('super_admin','content_moderation','resolve_report'),
    ('super_admin','content_moderation','remove_post'),
    ('super_admin','content_moderation','label_content'),
    ('super_admin','content_moderation','manage_community_notes'),
    ('super_admin','analytics_access','view_global_analytics'),
    ('super_admin','analytics_access','export_data'),
    ('super_admin','analytics_access','view_revenue'),
    ('super_admin','system_config','edit_settings'),
    ('super_admin','system_config','manage_feature_flags'),
    ('super_admin','system_config','manage_topics'),
    ('super_admin','system_config','manage_trending'),
    ('super_admin','advertising','view_campaigns'),
    ('super_admin','advertising','approve_ad'),
    ('super_admin','advertising','reject_ad'),
    ('super_admin','advertising','adjust_billing'),
    ('super_admin','security','view_auth_logs'),
    ('super_admin','security','force_logout'),
    ('super_admin','security','unlock_account'),
    ('super_admin','security','manage_2fa'),
    ('super_admin','billing','view_subscriptions'),
    ('super_admin','billing','issue_refund'),
    ('super_admin','billing','manage_coupons'),
    ('super_admin','trending_topics','pin_topic'),
    ('super_admin','trending_topics','remove_topic'),
    ('super_admin','trending_topics','promote_hashtag'),
    ('super_admin','trending_topics','ban_hashtag'),
    -- moderator
    ('moderator','user_management','view_users'),
    ('moderator','user_management','suspend_user'),
    ('moderator','content_moderation','view_reports'),
    ('moderator','content_moderation','resolve_report'),
    ('moderator','content_moderation','remove_post'),
    ('moderator','content_moderation','label_content'),
    ('moderator','content_moderation','manage_community_notes'),
    -- analyst
    ('analyst','analytics_access','view_global_analytics'),
    ('analyst','analytics_access','export_data'),
    ('analyst','analytics_access','view_revenue'),
    -- support
    ('support','user_management','view_users'),
    ('support','user_management','edit_user'),
    ('support','user_management','reset_password'),
    ('support','security','force_logout'),
    ('support','security','unlock_account'),
    ('support','billing','view_subscriptions'),
    -- advertiser_admin
    ('advertiser_admin','advertising','view_campaigns'),
    ('advertiser_admin','advertising','approve_ad'),
    ('advertiser_admin','advertising','reject_ad'),
    ('advertiser_admin','advertising','adjust_billing'),
    ('advertiser_admin','analytics_access','view_global_analytics'),
    -- content_curator
    ('content_curator','trending_topics','pin_topic'),
    ('content_curator','trending_topics','remove_topic'),
    ('content_curator','trending_topics','promote_hashtag'),
    ('content_curator','trending_topics','ban_hashtag'),
    ('content_curator','system_config','manage_topics'),
    ('content_curator','system_config','manage_trending');

-- ─────────────────────────────────────────────
--  A6. SUPPORT TICKETS
-- ─────────────────────────────────────────────
CREATE TABLE support_tickets (
    id                UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id           UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    assigned_to       UUID REFERENCES admins(id) ON DELETE SET NULL,
    status            VARCHAR(20) NOT NULL DEFAULT 'open',
    priority          VARCHAR(10) NOT NULL DEFAULT 'normal',
    category          VARCHAR(50) NOT NULL,
    subject           VARCHAR(280) NOT NULL,
    description       TEXT NOT NULL,
    resolution        TEXT,
    related_post_id   UUID REFERENCES posts(id) ON DELETE SET NULL,
    related_report_id UUID REFERENCES reports(id) ON DELETE SET NULL,
    metadata          JSONB NOT NULL DEFAULT '{}',
    created_at        TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    resolved_at       TIMESTAMPTZ
);
CREATE INDEX idx_tickets_user     ON support_tickets (user_id);
CREATE INDEX idx_tickets_assigned ON support_tickets (assigned_to, status);
CREATE INDEX idx_tickets_status   ON support_tickets (status, priority, created_at);

CREATE TABLE support_ticket_messages (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    ticket_id   UUID NOT NULL REFERENCES support_tickets(id) ON DELETE CASCADE,
    sender_id   UUID NOT NULL REFERENCES users(id),
    is_admin    BOOLEAN NOT NULL DEFAULT FALSE,
    is_internal BOOLEAN NOT NULL DEFAULT FALSE,
    content     TEXT NOT NULL,
    attachments JSONB NOT NULL DEFAULT '[]',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_ticket_msgs ON support_ticket_messages (ticket_id, created_at);

-- ─────────────────────────────────────────────
--  A7. FEATURE FLAGS
-- ─────────────────────────────────────────────
CREATE TABLE feature_flags (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    key         VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    is_enabled  BOOLEAN NOT NULL DEFAULT FALSE,
    rollout_pct NUMERIC(5,2) NOT NULL DEFAULT 0,
    conditions  JSONB NOT NULL DEFAULT '{}',
    updated_by  UUID REFERENCES admins(id),
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- ─────────────────────────────────────────────
--  A8. CONFIGURACIÓN GLOBAL
-- ─────────────────────────────────────────────
CREATE TABLE platform_config (
    key         VARCHAR(100) PRIMARY KEY,
    value       JSONB NOT NULL,
    description TEXT,
    is_secret   BOOLEAN NOT NULL DEFAULT FALSE,
    updated_by  UUID REFERENCES admins(id),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

INSERT INTO platform_config (key, value, description) VALUES
    ('max_post_length',       '{"free": 280, "premium": 4000}',               'Límite de caracteres por tier'),
    ('max_media_per_post',    '{"free": 4, "premium": 8}',                    'Archivos adjuntos por post'),
    ('rate_limits',           '{"posts_per_hour": 50, "dms_per_day": 500}',   'Rate limits globales'),
    ('maintenance_mode',      'false',                                         'Activar modo mantenimiento'),
    ('registration_open',     'true',                                          'Permitir nuevos registros'),
    ('trending_refresh_mins', '15',                                            'Frecuencia de actualización trending'),
    ('ad_load_ratio',         '{"timeline": 0.1, "search": 0.15}',            'Proporción de anuncios en feeds');

-- ─────────────────────────────────────────────
--  A9. HASHTAG ADMIN ACTIONS
-- ─────────────────────────────────────────────
CREATE TABLE hashtag_admin_actions (
    id         UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    hashtag_id UUID NOT NULL REFERENCES hashtags(id) ON DELETE CASCADE,
    action     VARCHAR(20) NOT NULL,
    reason     TEXT,
    admin_id   UUID NOT NULL REFERENCES admins(id),
    expires_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_hashtag_actions ON hashtag_admin_actions (hashtag_id, action);

-- ═══════════════════════════════════════════════════════════
--  TRIGGERS
-- ═══════════════════════════════════════════════════════════

-- Función shared para updated_at
CREATE OR REPLACE FUNCTION set_updated_at() RETURNS TRIGGER AS $$
BEGIN NEW.updated_at = NOW(); RETURN NEW; END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_users_updated_at         BEFORE UPDATE ON users            FOR EACH ROW EXECUTE FUNCTION set_updated_at();
CREATE TRIGGER trg_posts_updated_at         BEFORE UPDATE ON posts            FOR EACH ROW EXECUTE FUNCTION set_updated_at();
CREATE TRIGGER trg_subs_updated_at          BEFORE UPDATE ON subscriptions    FOR EACH ROW EXECUTE FUNCTION set_updated_at();
CREATE TRIGGER trg_campaigns_updated_at     BEFORE UPDATE ON ad_campaigns     FOR EACH ROW EXECUTE FUNCTION set_updated_at();
CREATE TRIGGER trg_lists_updated_at         BEFORE UPDATE ON lists            FOR EACH ROW EXECUTE FUNCTION set_updated_at();
CREATE TRIGGER trg_admins_updated_at        BEFORE UPDATE ON admins           FOR EACH ROW EXECUTE FUNCTION set_updated_at();
CREATE TRIGGER trg_tickets_updated_at       BEFORE UPDATE ON support_tickets  FOR EACH ROW EXECUTE FUNCTION set_updated_at();
CREATE TRIGGER trg_flags_updated_at         BEFORE UPDATE ON feature_flags    FOR EACH ROW EXECUTE FUNCTION set_updated_at();

-- Contadores de follows
CREATE OR REPLACE FUNCTION update_follow_counts() RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        UPDATE users SET following_count = following_count + 1 WHERE id = NEW.follower_id;
        UPDATE users SET followers_count = followers_count + 1 WHERE id = NEW.following_id;
    ELSIF TG_OP = 'DELETE' THEN
        UPDATE users SET following_count = GREATEST(0, following_count - 1) WHERE id = OLD.follower_id;
        UPDATE users SET followers_count = GREATEST(0, followers_count - 1) WHERE id = OLD.following_id;
    END IF;
    RETURN NULL;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_follow_counts
    AFTER INSERT OR DELETE ON follows
    FOR EACH ROW EXECUTE FUNCTION update_follow_counts();

-- Contador de likes
CREATE OR REPLACE FUNCTION update_like_count() RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        UPDATE posts SET likes_count = likes_count + 1               WHERE id = NEW.post_id;
        UPDATE users SET likes_given_count = likes_given_count + 1   WHERE id = NEW.user_id;
    ELSIF TG_OP = 'DELETE' THEN
        UPDATE posts SET likes_count = GREATEST(0, likes_count - 1)              WHERE id = OLD.post_id;
        UPDATE users SET likes_given_count = GREATEST(0, likes_given_count - 1)  WHERE id = OLD.user_id;
    END IF;
    RETURN NULL;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_like_count
    AFTER INSERT OR DELETE ON reactions
    FOR EACH ROW EXECUTE FUNCTION update_like_count();

-- Contador de posts por usuario
CREATE OR REPLACE FUNCTION update_posts_count() RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' AND NEW.deleted_at IS NULL THEN
        UPDATE users SET posts_count = posts_count + 1 WHERE id = NEW.author_id;
        IF NEW.post_type = 'essay' THEN
            UPDATE users SET essays_count = essays_count + 1 WHERE id = NEW.author_id;
        END IF;
    ELSIF TG_OP = 'UPDATE' AND NEW.deleted_at IS NOT NULL AND OLD.deleted_at IS NULL THEN
        UPDATE users SET posts_count = GREATEST(0, posts_count - 1) WHERE id = NEW.author_id;
    END IF;
    RETURN NULL;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_posts_count
    AFTER INSERT OR UPDATE ON posts
    FOR EACH ROW EXECUTE FUNCTION update_posts_count();

-- Contador de hashtags
CREATE OR REPLACE FUNCTION update_hashtag_count() RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        UPDATE hashtags SET posts_count = posts_count + 1 WHERE id = NEW.hashtag_id;
    ELSIF TG_OP = 'DELETE' THEN
        UPDATE hashtags SET posts_count = GREATEST(0, posts_count - 1) WHERE id = OLD.hashtag_id;
    END IF;
    RETURN NULL;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_hashtag_count
    AFTER INSERT OR DELETE ON post_hashtags
    FOR EACH ROW EXECUTE FUNCTION update_hashtag_count();

-- tsvector de posts
CREATE OR REPLACE FUNCTION update_post_tsvec() RETURNS TRIGGER AS $$
BEGIN
    NEW.content_tsvec := to_tsvector('spanish',
        COALESCE(NEW.content,'') || ' ' || COALESCE(NEW.essay_title,''));
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_post_tsvec
    BEFORE INSERT OR UPDATE OF content, essay_title ON posts
    FOR EACH ROW EXECUTE FUNCTION update_post_tsvec();

-- ═══════════════════════════════════════════════════════════
--  VISTAS
-- ═══════════════════════════════════════════════════════════
CREATE VIEW v_posts_public AS
SELECT
    p.id, p.post_type, p.content, p.essay_title,
    p.published_at, p.likes_count, p.reposts_count,
    p.replies_count, p.quotes_count, p.views_count,
    p.bookmarks_count, p.link_preview, p.geo,
    u.id            AS author_id,
    u.username      AS author_username,
    u.display_name  AS author_display_name,
    u.profile_image_url AS author_avatar,
    u.subscription_tier AS author_tier,
    EXISTS (
        SELECT 1 FROM user_badges ub
        WHERE ub.user_id = u.id AND ub.badge = 'verified'
    ) AS author_is_verified,
    p.metadata
FROM posts p
JOIN users u ON u.id = p.author_id
WHERE p.deleted_at IS NULL
  AND u.deleted_at IS NULL
  AND u.status = 'active';

CREATE VIEW v_trending_hashtags AS
SELECT
    h.tag,
    COUNT(ph.post_id) AS recent_posts,
    h.posts_count     AS all_time_posts
FROM hashtags h
JOIN post_hashtags ph ON ph.hashtag_id = h.id
JOIN posts p          ON p.id = ph.post_id
WHERE p.published_at > NOW() - INTERVAL '24 hours'
  AND p.deleted_at IS NULL
GROUP BY h.id
ORDER BY recent_posts DESC;

CREATE VIEW v_admin_dashboard AS
SELECT
    a.id AS admin_id,
    u.username, u.display_name, u.email, u.profile_image_url,
    a.role, a.is_active, a.granted_at, a.access_config,
    COUNT(DISTINCT al.id)  AS total_actions,
    MAX(al.created_at)     AS last_action_at,
    COUNT(DISTINCT s.id)
        FILTER (WHERE s.is_active AND s.expires_at > NOW())
                           AS active_sessions
FROM admins a
JOIN users u              ON u.id = a.user_id
LEFT JOIN admin_audit_log al ON al.admin_id = a.id
LEFT JOIN admin_sessions s   ON s.admin_id  = a.id
GROUP BY a.id, u.id;

-- ═══════════════════════════════════════════════════════════
--  FUNCIÓN: verificar permiso de admin
-- ═══════════════════════════════════════════════════════════
CREATE OR REPLACE FUNCTION admin_has_permission(
    p_admin_id UUID,
    p_category admin_action_category,
    p_action   VARCHAR
) RETURNS BOOLEAN AS $$
DECLARE
    v_role      admin_role;
    v_is_active BOOLEAN;
    v_override  BOOLEAN;
    v_default   BOOLEAN;
BEGIN
    SELECT role, is_active INTO v_role, v_is_active
    FROM admins WHERE id = p_admin_id;
    IF NOT FOUND OR NOT v_is_active THEN RETURN FALSE; END IF;
    IF v_role = 'super_admin' THEN RETURN TRUE; END IF;

    SELECT is_granted INTO v_override
    FROM admin_permissions
    WHERE admin_id = p_admin_id AND category = p_category AND action = p_action;
    IF FOUND THEN RETURN v_override; END IF;

    SELECT is_granted INTO v_default
    FROM admin_role_defaults
    WHERE role = v_role AND category = p_category AND action = p_action;
    RETURN COALESCE(v_default, FALSE);
END;
$$ LANGUAGE plpgsql STABLE;

-- ═══════════════════════════════════════════════════════════
--  ROW LEVEL SECURITY
-- ═══════════════════════════════════════════════════════════
ALTER TABLE users         ENABLE ROW LEVEL SECURITY;
ALTER TABLE posts         ENABLE ROW LEVEL SECURITY;
ALTER TABLE dm_messages   ENABLE ROW LEVEL SECURITY;
ALTER TABLE notifications ENABLE ROW LEVEL SECURITY;
ALTER TABLE bookmarks     ENABLE ROW LEVEL SECURITY;

CREATE POLICY users_own_row ON users
    USING (id = current_setting('app.current_user_id', TRUE)::UUID);

CREATE POLICY posts_public_or_own ON posts
    USING (audience = 'public'
        OR author_id = current_setting('app.current_user_id', TRUE)::UUID);

CREATE POLICY dm_participants_only ON dm_messages
    USING (conversation_id IN (
        SELECT conversation_id FROM dm_participants
        WHERE user_id = current_setting('app.current_user_id', TRUE)::UUID
    ));

CREATE POLICY notif_own ON notifications
    USING (recipient_id = current_setting('app.current_user_id', TRUE)::UUID);

CREATE POLICY bookmarks_own ON bookmarks
    USING (user_id = current_setting('app.current_user_id', TRUE)::UUID);
```