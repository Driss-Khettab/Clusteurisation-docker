# --- base: shared by the stages below ---
FROM node:24-alpine AS base
WORKDIR /app
COPY package.json yarn.lock ./

# --- build: all deps (TypeScript...) to compile src/ into dist/ ---
FROM base AS build
RUN yarn install --frozen-lockfile
COPY tsconfig.json ./
COPY src ./src
RUN yarn build

# --- prod-deps: runtime dependencies only ---
# yarn v1 keeps devDependencies that are also optional peers of a prod dep
# (typeorm -> ts-node -> typescript, ~25 MB) even with --production, so we
# drop devDependencies from package.json first; yarn.lock still pins versions.
FROM base AS prod-deps
RUN npm pkg delete devDependencies \
  && yarn install --production --non-interactive \
  && yarn cache clean

# --- runtime: only node, prod node_modules and compiled JS ---
FROM node:24-alpine AS runtime
ENV NODE_ENV=production
WORKDIR /app
COPY package.json ./
COPY --from=prod-deps /app/node_modules ./node_modules
COPY --from=build /app/dist ./dist
USER node
EXPOSE 3000
CMD ["node", "dist/index.js"]
