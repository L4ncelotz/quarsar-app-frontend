<template>
  <q-page padding>
    <div class="text-h4 q-mb-md">
      Advanced Full-Stack Demo (Quasar + Express)
    </div>
    <div class="text-subtitle1 q-mb-md text-grey-7">
      รหัส: 6604101403 | ชื่อ: ชยานันต์ หล้ากาศ
    </div>

    <!-- Git Workflow (จากตัวอย่างก่อน) -->
    <q-card class="q-mb-md">
      <q-card-section>
        <div class="text-h6">Git Workflow</div>
        <q-list bordered separator class="q-mt-sm">
          <q-item v-for="(step, index) in gitSteps" :key="index">
            <q-item-section avatar>
              <q-badge>{{ index + 1 }}</q-badge>
            </q-item-section>
            <q-item-section>
              <q-item-label>{{ step.title }}</q-item-label>
              <q-item-label caption>{{ step.detail }}</q-item-label>
            </q-item-section>
          </q-item>
        </q-list>
      </q-card-section>
    </q-card>

    <!-- Docker Concepts (จากตัวอย่างก่อน) -->
    <q-card class="q-mb-md">
      <q-card-section>
        <div class="text-h6">Docker Concepts</div>
        <q-list bordered separator class="q-mt-sm">
          <q-item v-for="(item, index) in dockerItems" :key="index">
            <q-item-section>
              <q-item-label>{{ item.title }}</q-item-label>
              <q-item-label caption>{{ item.detail }}</q-item-label>
            </q-item-section>
          </q-item>
        </q-list>
      </q-card-section>
    </q-card>

    <!-- New: API Data from Backend -->
    <q-card>
      <q-card-section>
        <div class="text-h6">Data from Backend API</div>
        <div v-if="loading" class="text-center q-pa-md">
          <q-spinner color="primary" size="3em" />
          <div class="q-mt-sm">Loading data from backend...</div>
        </div>
        <div v-else-if="error" class="text-negative">
          <q-icon name="error" size="2em" />
          {{ error }}
        </div>
        <div v-else>
          <q-list bordered separator class="q-mt-sm">
            <q-item>
              <q-item-section>
                <q-item-label>{{ apiData.git.title }}</q-item-label>
                <q-item-label caption>{{ apiData.git.detail }}</q-item-label>
              </q-item-section>
            </q-item>
            <q-item>
              <q-item-section>
                <q-item-label>{{ apiData.docker.title }}</q-item-label>
                <q-item-label caption>{{ apiData.docker.detail }}</q-item-label>
              </q-item-section>
            </q-item>
          </q-list>
          <q-btn 
            color="primary" 
            @click="fetchData" 
            class="q-mt-md"
            icon="refresh"
            label="Refresh Data"
          />
        </div>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

// จากตัวอย่างก่อน
const gitSteps = [
  {
    title: 'แก้โค้ด Quasar ใน src/',
    detail: 'เพิ่มหน้า / component ใหม่ เช่น IndexPage.vue, Layout ต่าง ๆ',
  },
  {
    title: 'ตรวจสอบสถานะไฟล์',
    detail: 'ใช้คำสั่ง `git status` ดูว่าไฟล์ไหนเปลี่ยนแปลงบ้าง',
  },
  {
    title: 'เพิ่มไฟล์เข้า staging',
    detail: 'ใช้ `git add .` หรือ `git add src/pages/IndexPage.vue`',
  },
  {
    title: 'สร้าง commit พร้อมข้อความแบบ Conventional Commits',
    detail: 'เช่น `feat: add Git workflow demo page`',
  },
  {
    title: 'push ขึ้น GitHub',
    detail: 'ใช้ `git push origin feature/quasar-demo` แล้วเปิด Pull Request',
  },
];

const dockerItems = [
  {
    title: 'Image',
    detail: 'แม่พิมพ์ของ container สร้างจาก Dockerfile เช่น image สำหรับ Quasar SPA',
  },
  {
    title: 'Container',
    detail: 'instance ของ image ที่กำลังรัน เช่น container ที่รัน Nginx เสิร์ฟไฟล์ของ Quasar',
  },
  {
    title: 'Volume',
    detail: 'ใช้เก็บข้อมูลถาวร เช่น log / ไฟล์ upload (frontend อาจไม่ใช้มากเท่า backend)',
  },
  {
    title: 'Network',
    detail: 'เชื่อม container ระหว่างกัน เช่น frontend คุยกับ backend ผ่าน network ของ Docker',
  },
];

// API Integration
const apiData = ref({ git: { title: '', detail: '' }, docker: { title: '', detail: '' } });
const loading = ref(true);
const error = ref(null);

const fetchData = async () => {
  loading.value = true;
  error.value = null;
  try {
    const apiUrl = import.meta.env.VITE_API_URL || 'http://localhost:3000';
    const response = await axios.get(`${apiUrl}/api/demo`);
    apiData.value = response.data;
  } catch (err) {
    console.error('API Error:', err);
    error.value = `Failed to fetch data: ${err.message}`;
  } finally {
    loading.value = false;
  }
};

onMounted(fetchData);
</script>