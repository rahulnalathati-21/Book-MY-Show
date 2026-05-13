# =========================
# Build Stage
# =========================

FROM node:18 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install --legacy-peer-deps

COPY . .

ENV NODE_OPTIONS=--openssl-legacy-provider

RUN npm run build

# =========================
# Production Stage
# =========================

FROM nginx:alpine

COPY --from=builder /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
