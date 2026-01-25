# Conventions & Bonnes Pratiques Redux

## 📋 Conventions de Nommage

### Structure des fichiers (Redux Toolkit)

```
src/
├── app/
│   └── store.ts                 # Configuration du store
├── features/
│   ├── auth/
│   │   ├── authSlice.ts        # Slice
│   │   ├── authThunks.ts       # Thunks async
│   │   └── authSelectors.ts    # Selectors
│   ├── users/
│   │   ├── usersSlice.ts
│   │   └── usersApi.ts         # RTK Query
│   └── posts/
│       ├── postsSlice.ts
│       └── postsSelectors.ts
```

### Nommage des actions

```typescript
// Pattern: feature/action
// ✅ Bon
const increment = createAction("counter/increment");
const userLoggedIn = createAction("auth/userLoggedIn");
const postAdded = createAction("posts/postAdded");

// ❌ Éviter
const inc = createAction("INC");
const login = createAction("LOGIN");
```

---

## 🏗️ Redux Toolkit (Recommandé)

### Configuration du Store

```typescript
// app/store.ts
import { configureStore } from "@reduxjs/toolkit";
import authReducer from "@/features/auth/authSlice";
import postsReducer from "@/features/posts/postsSlice";
import { apiSlice } from "@/features/api/apiSlice";

export const store = configureStore({
  reducer: {
    auth: authReducer,
    posts: postsReducer,
    [apiSlice.reducerPath]: apiSlice.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(apiSlice.middleware),
  devTools: process.env.NODE_ENV !== "production",
});

// Types
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### Hooks typés

```typescript
// app/hooks.ts
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";
import type { RootState, AppDispatch } from "./store";

export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

### Provider

```typescript
// App.tsx
import { Provider } from 'react-redux';
import { store } from './app/store';

function App() {
  return (
    <Provider store={store}>
      {/* App content */}
    </Provider>
  );
}
```

---

## 📦 Slices (createSlice)

### Structure de base

```typescript
// features/counter/counterSlice.ts
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

interface CounterState {
  value: number;
  status: "idle" | "loading" | "failed";
}

const initialState: CounterState = {
  value: 0,
  status: "idle",
};

export const counterSlice = createSlice({
  name: "counter",
  initialState,
  reducers: {
    increment: (state) => {
      // ✅ Immer permet les mutations
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action: PayloadAction<number>) => {
      state.value += action.payload;
    },
    reset: (state) => {
      state.value = 0;
    },
  },
});

export const { increment, decrement, incrementByAmount, reset } =
  counterSlice.actions;
export default counterSlice.reducer;
```

### Slice avec données complexes

```typescript
// features/posts/postsSlice.ts
import { createSlice, PayloadAction, nanoid } from "@reduxjs/toolkit";

interface Post {
  id: string;
  title: string;
  content: string;
  author: string;
  createdAt: string;
  reactions: {
    thumbsUp: number;
    hooray: number;
  };
}

interface PostsState {
  posts: Post[];
  status: "idle" | "loading" | "succeeded" | "failed";
  error: string | null;
}

const initialState: PostsState = {
  posts: [],
  status: "idle",
  error: null,
};

export const postsSlice = createSlice({
  name: "posts",
  initialState,
  reducers: {
    postAdded: {
      reducer(state, action: PayloadAction<Post>) {
        state.posts.push(action.payload);
      },
      // Prepare permet de générer l'ID avant le reducer
      prepare(title: string, content: string, author: string) {
        return {
          payload: {
            id: nanoid(),
            title,
            content,
            author,
            createdAt: new Date().toISOString(),
            reactions: { thumbsUp: 0, hooray: 0 },
          },
        };
      },
    },
    postUpdated: (
      state,
      action: PayloadAction<{ id: string; title: string; content: string }>,
    ) => {
      const { id, title, content } = action.payload;
      const existingPost = state.posts.find((post) => post.id === id);
      if (existingPost) {
        existingPost.title = title;
        existingPost.content = content;
      }
    },
    postDeleted: (state, action: PayloadAction<string>) => {
      state.posts = state.posts.filter((post) => post.id !== action.payload);
    },
    reactionAdded: (
      state,
      action: PayloadAction<{
        postId: string;
        reaction: keyof Post["reactions"];
      }>,
    ) => {
      const { postId, reaction } = action.payload;
      const existingPost = state.posts.find((post) => post.id === postId);
      if (existingPost) {
        existingPost.reactions[reaction]++;
      }
    },
  },
});

export const { postAdded, postUpdated, postDeleted, reactionAdded } =
  postsSlice.actions;
export default postsSlice.reducer;
```

---

## 🔄 Thunks Asynchrones

### createAsyncThunk

```typescript
// features/posts/postsSlice.ts
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";
import axios from "axios";

// Thunk
export const fetchPosts = createAsyncThunk("posts/fetchPosts", async () => {
  const response = await axios.get("/api/posts");
  return response.data;
});

export const addNewPost = createAsyncThunk(
  "posts/addNewPost",
  async (initialPost: { title: string; content: string }) => {
    const response = await axios.post("/api/posts", initialPost);
    return response.data;
  },
);

// Avec gestion d'erreur
export const deletePost = createAsyncThunk(
  "posts/deletePost",
  async (postId: string, { rejectWithValue }) => {
    try {
      await axios.delete(`/api/posts/${postId}`);
      return postId;
    } catch (err: any) {
      return rejectWithValue(err.response.data);
    }
  },
);

// Slice avec extraReducers
export const postsSlice = createSlice({
  name: "posts",
  initialState,
  reducers: {
    // Reducers synchrones
  },
  extraReducers: (builder) => {
    builder
      // Fetch posts
      .addCase(fetchPosts.pending, (state) => {
        state.status = "loading";
      })
      .addCase(fetchPosts.fulfilled, (state, action) => {
        state.status = "succeeded";
        state.posts = action.payload;
      })
      .addCase(fetchPosts.rejected, (state, action) => {
        state.status = "failed";
        state.error = action.error.message || "Failed to fetch posts";
      })
      // Add post
      .addCase(addNewPost.fulfilled, (state, action) => {
        state.posts.push(action.payload);
      })
      // Delete post
      .addCase(deletePost.fulfilled, (state, action) => {
        state.posts = state.posts.filter((post) => post.id !== action.payload);
      });
  },
});
```

### Utilisation dans les composants

```typescript
import { useEffect } from 'react';
import { useAppDispatch, useAppSelector } from '@/app/hooks';
import { fetchPosts, addNewPost } from './postsSlice';

function PostsList() {
  const dispatch = useAppDispatch();
  const posts = useAppSelector((state) => state.posts.posts);
  const status = useAppSelector((state) => state.posts.status);
  const error = useAppSelector((state) => state.posts.error);

  useEffect(() => {
    if (status === 'idle') {
      dispatch(fetchPosts());
    }
  }, [status, dispatch]);

  const handleAddPost = async () => {
    try {
      await dispatch(addNewPost({ title: 'New', content: 'Content' })).unwrap();
      // Success
    } catch (err) {
      // Error handling
    }
  };

  if (status === 'loading') return <div>Loading...</div>;
  if (status === 'failed') return <div>Error: {error}</div>;

  return (
    <div>
      {posts.map((post) => (
        <div key={post.id}>{post.title}</div>
      ))}
      <button onClick={handleAddPost}>Add Post</button>
    </div>
  );
}
```

---

## 🔍 Selectors

### Selectors de base

```typescript
// features/posts/postsSelectors.ts
import { RootState } from "@/app/store";

export const selectAllPosts = (state: RootState) => state.posts.posts;
export const selectPostById = (state: RootState, postId: string) =>
  state.posts.posts.find((post) => post.id === postId);
export const selectPostsStatus = (state: RootState) => state.posts.status;
export const selectPostsError = (state: RootState) => state.posts.error;
```

### Selectors mémoïsés (createSelector)

```typescript
import { createSelector } from '@reduxjs/toolkit';
import { RootState } from '@/app/store';

export const selectAllPosts = (state: RootState) => state.posts.posts;

// Mémoïsé - ne recalcule que si posts change
export const selectPostsByUser = createSelector(
  [selectAllPosts, (state: RootState, userId: string) => userId],
  (posts, userId) => posts.filter((post) => post.author === userId)
);

export const selectPostsCount = createSelector(
  [selectAllPosts],
  (posts) => posts.length
);

// Tri mémoïsé
export const selectSortedPosts = createSelector(
  [selectAllPosts],
  (posts) => posts.slice().sort((a, b) => b.createdAt.localeCompare(a.createdAt))
);

// Utilisation
function UserPosts({ userId }: { userId: string }) {
  const userPosts = useAppSelector((state) => selectPostsByUser(state, userId));
  return <div>{/* render */}</div>;
}
```

---

## 🌐 RTK Query (API Caching)

### Configuration de l'API

```typescript
// features/api/apiSlice.ts
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

interface Post {
  id: string;
  title: string;
  content: string;
}

export const apiSlice = createApi({
  reducerPath: "api",
  baseQuery: fetchBaseQuery({
    baseUrl: "/api",
    prepareHeaders: (headers, { getState }) => {
      const token = (getState() as RootState).auth.token;
      if (token) {
        headers.set("Authorization", `Bearer ${token}`);
      }
      return headers;
    },
  }),
  tagTypes: ["Post", "User"],
  endpoints: (builder) => ({
    // Query (GET)
    getPosts: builder.query<Post[], void>({
      query: () => "/posts",
      providesTags: ["Post"],
    }),

    getPost: builder.query<Post, string>({
      query: (id) => `/posts/${id}`,
      providesTags: (result, error, id) => [{ type: "Post", id }],
    }),

    // Mutation (POST, PUT, DELETE)
    addPost: builder.mutation<Post, Partial<Post>>({
      query: (body) => ({
        url: "/posts",
        method: "POST",
        body,
      }),
      invalidatesTags: ["Post"],
    }),

    updatePost: builder.mutation<Post, Partial<Post> & { id: string }>({
      query: ({ id, ...patch }) => ({
        url: `/posts/${id}`,
        method: "PUT",
        body: patch,
      }),
      invalidatesTags: (result, error, { id }) => [{ type: "Post", id }],
    }),

    deletePost: builder.mutation<void, string>({
      query: (id) => ({
        url: `/posts/${id}`,
        method: "DELETE",
      }),
      invalidatesTags: ["Post"],
    }),
  }),
});

export const {
  useGetPostsQuery,
  useGetPostQuery,
  useAddPostMutation,
  useUpdatePostMutation,
  useDeletePostMutation,
} = apiSlice;
```

### Utilisation dans les composants

```typescript
import { useGetPostsQuery, useAddPostMutation } from './apiSlice';

function PostsList() {
  const { data: posts, isLoading, isError, error } = useGetPostsQuery();
  const [addPost, { isLoading: isAdding }] = useAddPostMutation();

  const handleAdd = async () => {
    try {
      await addPost({ title: 'New', content: 'Content' }).unwrap();
    } catch (err) {
      console.error('Failed to add post:', err);
    }
  };

  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>Error: {error.toString()}</div>;

  return (
    <div>
      {posts?.map((post) => (
        <div key={post.id}>{post.title}</div>
      ))}
      <button onClick={handleAdd} disabled={isAdding}>
        Add Post
      </button>
    </div>
  );
}

// Avec paramètres
function PostDetail({ postId }: { postId: string }) {
  const { data: post, isLoading } = useGetPostQuery(postId);

  if (isLoading) return <div>Loading...</div>;
  if (!post) return <div>Post not found</div>;

  return <div>{post.title}</div>;
}

// Polling
function LivePosts() {
  const { data: posts } = useGetPostsQuery(undefined, {
    pollingInterval: 3000, // Refetch toutes les 3s
  });

  return <div>{/* render */}</div>;
}

// Skip conditionnel
function ConditionalPost({ shouldFetch, postId }: any) {
  const { data } = useGetPostQuery(postId, {
    skip: !shouldFetch,
  });

  return <div>{/* render */}</div>;
}
```

---

## 🎯 Best Practices

### 1. Normaliser les données

```typescript
// ❌ Éviter - données imbriquées
interface State {
  posts: {
    id: string;
    title: string;
    author: {
      id: string;
      name: string;
    };
    comments: {
      id: string;
      text: string;
    }[];
  }[];
}

// ✅ Bon - données normalisées
import { createEntityAdapter } from "@reduxjs/toolkit";

const postsAdapter = createEntityAdapter<Post>();
const usersAdapter = createEntityAdapter<User>();
const commentsAdapter = createEntityAdapter<Comment>();

interface State {
  posts: ReturnType<typeof postsAdapter.getInitialState>;
  users: ReturnType<typeof usersAdapter.getInitialState>;
  comments: ReturnType<typeof commentsAdapter.getInitialState>;
}
```

### 2. Entity Adapter

```typescript
import { createEntityAdapter, createSlice } from "@reduxjs/toolkit";

interface Post {
  id: string;
  title: string;
  content: string;
}

const postsAdapter = createEntityAdapter<Post>({
  // Tri par date de création
  sortComparer: (a, b) => b.createdAt.localeCompare(a.createdAt),
});

const initialState = postsAdapter.getInitialState({
  status: "idle" as "idle" | "loading" | "succeeded" | "failed",
  error: null as string | null,
});

export const postsSlice = createSlice({
  name: "posts",
  initialState,
  reducers: {
    postAdded: postsAdapter.addOne,
    postsReceived: postsAdapter.setAll,
    postUpdated: postsAdapter.updateOne,
    postDeleted: postsAdapter.removeOne,
  },
});

// Selectors
export const {
  selectAll: selectAllPosts,
  selectById: selectPostById,
  selectIds: selectPostIds,
} = postsAdapter.getSelectors((state: RootState) => state.posts);
```

### 3. Éviter les données dérivées dans le state

```typescript
// ❌ Éviter
interface State {
  posts: Post[];
  postsCount: number;        // Dérivé de posts
  sortedPosts: Post[];       // Dérivé de posts
}

// ✅ Bon - utiliser selectors
interface State {
  posts: Post[];
}

export const selectPostsCount = createSelector(
  [selectAllPosts],
  (posts) => posts.length
);

export const selectSortedPosts = createSelector(
  [selectAllPosts],
  (posts) => posts.slice().sort(...)
);
```

### 4. State minimal

```typescript
// ❌ Éviter - trop de state
interface State {
  posts: Post[];
  currentPost: Post | null;
  editingPost: Post | null;
  filteredPosts: Post[];
  searchTerm: string;
}

// ✅ Bon - state minimal
interface State {
  posts: Post[];
  currentPostId: string | null;
  editingPostId: string | null;
  searchTerm: string;
}
```

---

## 🔧 Middleware

### Middleware personnalisé

```typescript
import { Middleware } from "@reduxjs/toolkit";

const loggerMiddleware: Middleware = (store) => (next) => (action) => {
  console.log("dispatching", action);
  const result = next(action);
  console.log("next state", store.getState());
  return result;
};

export const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(loggerMiddleware),
});
```

---

## 📚 Ressources

- [Redux Toolkit Documentation](https://redux-toolkit.js.org/)
- [RTK Query](https://redux-toolkit.js.org/rtk-query/overview)
- [Redux Style Guide](https://redux.js.org/style-guide/)
- [Redux Essentials Tutorial](https://redux.js.org/tutorials/essentials/part-1-overview-concepts)
