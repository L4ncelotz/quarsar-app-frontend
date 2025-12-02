# Stage 1: Build Quasar SPA
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files เพื่อ install dependencies ก่อน (ใช้ Cache layer)
COPY quasar-express-app/package*.json ./
RUN npm ci --legacy-peer-deps --ignore-scripts

# Copy source code ทั้งหมด
COPY quasar-express-app/ .

# Build Quasar (ตรวจสอบให้แน่ใจว่า script "build" ใน package.json รันคำสั่ง "quasar build")
RUN npm run build

# Stage 2: Production (Nginx serve static files)
FROM nginx:1.27-alpine

# Copy ไฟล์ที่ build เสร็จแล้วไปที่โฟลเดอร์ของ Nginx
# หมายเหตุ: ตรวจสอบ path output ใน quasar.config.js ว่าเป็น dist/spa หรือไม่ (ค่า default คือใช่)
COPY --from=builder /app/dist/spa /usr/share/nginx/html

# Copy ไฟล์ตั้งค่า Nginx เพื่อแก้ปัญหา 404 เวลา Refresh หน้าเว็บ
COPY nginx.conf /etc/nginx/conf.d/default.conf
# Expose port 80
EXPOSE 80

# Run Nginx
CMD ["nginx", "-g", "daemon off;"]