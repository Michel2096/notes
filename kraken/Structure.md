![[Pasted image 20260402213603.png]]

```
kraken-frontend/src/

├── lib/
│   └── api/
│       ├── client.ts          ← CRÍTICO: axios con baseURL=http://localhost:7001
│       │                         interceptor para JWT en headers
│       │                         interceptor de respuesta para refresh token
│       ├── auth.ts            ← login, register, logout, forgotPassword, verify2FA
│       ├── posts.ts           ← getFeed, getPost, createPost, react, bookmark
│       ├── users.ts           ← getProfile, updateProfile, follow, block, mute
│       ├── notifications.ts   ← getNotifications, markRead
│       ├── dm.ts              ← getConversations, getMessages, sendMessage
│       ├── communities.ts     ← getCommunities, joinCommunity
│       ├── essays.ts          ← getEssays, getEssay, subscribeToAuthor
│       ├── admin.ts           ← dashboard, manageUsers, resolveReport
│       └── media.ts           ← uploadMedia (multipart/form-data)

├── features/
│   ├── auth/
│   │   ├── authSlice.ts       ← guarda: { user, token, isAuthenticated }
│   │   │                         token se sincroniza con localStorage
│   │   ├── useAuth.ts
│   │   └── AuthGuard.tsx      ← redirige a /login si no hay token
│   │
│   ├── posts/
│   │   ├── postsSlice.ts      ← cache de posts por id
│   │   └── usePosts.ts
│   │
│   └── notifications/
│       ├── notificationsSlice.ts
│       └── useNotifications.ts

├── hooks/
│   ├── useInfiniteScroll.ts   ← para el feed con paginación por cursor
│   └── useWebSocket.ts        ← para DMs y notificaciones en tiempo real
│                                 conecta a ws://localhost:7001/ws

├── pages/
│   ├── Home/                  ✅ landing pública
│   ├── Login/                 ✅
│   ├── Register/              ✅
│   ├── ForgotPassword/        ← NUEVO (cubre password_resets)
│   │
│   ├── Feed/                  ← RENOMBRAR desde Posts/
│   │   └── Feed.tsx           ← feed principal autenticado
│   │
│   ├── Posts/
│   │   ├── PostDetail.tsx
│   │   └── Bookmarks.tsx
│   │
│   ├── Essays/
│   │   ├── EssayList.tsx
│   │   └── EssayReader.tsx
│   │
│   ├── Trends/
│   │   ├── TrendingFeed.tsx   ← usa v_trending_hashtags de tu SQL
│   │   └── HashtagPage.tsx
│   │
│   ├── Profile/
│   │   ├── ProfilePage.tsx    ← ruta: /@:username
│   │   ├── ProfileEdit.tsx
│   │   └── FollowersList.tsx
│   │
│   ├── Social/
│   │   ├── Notifications.tsx
│   │   ├── DirectMessages.tsx
│   │   └── Spaces.tsx         ← AGREGAR (cubre tabla spaces)
│   │
│   ├── Communities/
│   │   ├── Communities.tsx
│   │   └── CommunityDetail.tsx
│   │
│   ├── Settings/
│   │   ├── SettingsPage.tsx   ← edita users.settings JSONB
│   │   ├── PrivacySettings.tsx
│   │   └── NotificationSettings.tsx
│   │
│   └── Admin/                 ← MOVER desde raíz, rutas protegidas por rol
│       ├── AdminDashboard.tsx
│       ├── AdminUsers.tsx
│       ├── AdminModeration.tsx
│       └── AdminSettings.tsx

└── routes/
    ├── AppRoutes.tsx          ← define todas las rutas
    ├── PrivateRoute.tsx       ← requiere token válido
    └── AdminRoute.tsx         ← NUEVO: requiere admins.is_active = true
```











[[Kraken]]

