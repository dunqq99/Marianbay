FROM node:18-alpine

WORKDIR /app

# Copy package.json and package-lock.json
COPY package.json package-lock.json ./

# Install dependencies
RUN npm ci

# Copy the rest of the application
COPY . .

# Set environment variables
ENV NODE_ENV production
ENV TZ Asia/Ho_Chi_Minh

EXPOSE 8082

CMD ["node", "server.js"]