# vue-client

Vue 2 single-page app for user authentication: log in, register, and browse a list of users. It talks to a separate REST API backend.

## Stack

- Vue 2 + Vue Router (history mode) + Vuex (namespaced modules)
- Axios for HTTP, with request/response interceptors
- VeeValidate for form validation
- Bootstrap-Vue and Vue Material for UI
- vue-toasted for error notifications
- Vue CLI 3 (Babel, ESLint), SCSS

## Requirements

- Node.js and Yarn
- The backend API running at `http://localhost:80/api/v1`

The API base URL is hard-coded in [src/main.js](src/main.js) (`Axios.defaults.baseURL`). Change it there to point at a different backend.

## Getting started

```bash
yarn install
yarn serve    # dev server with hot reload
yarn build    # production build into dist/
yarn lint     # lint and fix files
```

## Routes

| Path            | View                                         | Access                                 |
| --------------- | -------------------------------------------- | -------------------------------------- |
| `/login`        | [Login.vue](src/views/Login.vue)             | Guests only; logged-in users go to `/users` |
| `/registration` | [Registration.vue](src/views/Registration.vue) | Guests only; logged-in users go to `/users` |
| `/users`        | [UserList.vue](src/views/UserList.vue)       | Requires a token; otherwise go to `/login` |

`/` and any unknown path redirect to `/login`. The guards live in [src/router/routes.js](src/router/routes.js).

## API endpoints used

All paths are relative to the base URL.

| Method | Path        | Used by            | Request body                                 | Expected response          |
| ------ | ----------- | ------------------ | -------------------------------------------- | -------------------------- |
| POST   | `/login`    | Login              | `{ email, password }`                        | `{ token }`                |
| POST   | `/register` | Registration       | `{ firstName, lastName, email, password }`   | `{ token }`                |
| GET    | `/users`    | User list          | none                                         | `{ responsData: [...] }`   |

## Authentication

- On login or registration the returned token is saved to `localStorage` under the `accessToken` key and put in the Vuex `auth` module.
- Before each route change the token is reloaded from `localStorage`, so a page refresh keeps the user logged in.
- The request interceptor sends the token as the raw `Authorization` header (no `Bearer` prefix).
- The response interceptor shows a toast for any error response.
- Logging out removes the token from `localStorage`.

## Project structure

```
src/
  main.js              app bootstrap, plugins, API base URL
  App.vue              root component
  components/          shared components (header)
  views/               Login, Registration, UserList pages
  router/routes.js     routes and auth guards
  store/               Vuex store: auth, user, users modules
  services/            Axios wrappers (generic CRUD + auth)
  helpers/             Axios interceptors
  constants/           storage keys, messages
  utils/toasted.js     global toast definitions
```

## Configuration

See the [Vue CLI configuration reference](https://cli.vuejs.org/config/).
