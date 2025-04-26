<template>
  <div class="flex flex-col h-screen bg-gray-100">
    <!-- Header -->
    <header
      class="flex items-center justify-between px-6 py-3 bg-white shadow z-10"
    >
      <div class="flex items-center space-x-2">
        <img src="@/assets/vue.svg" alt="Logo" class="w-8 h-8" />
        <span class="font-bold text-lg text-gray-800">直播到臉書社團</span>
      </div>
      <div class="flex items-center space-x-4">
        <!-- 可以放平台圖示 -->
        <button
          class="bg-blue-600 text-white px-4 py-2 rounded font-bold hover:bg-blue-700 shadow"
        >
          Go live
        </button>
      </div>
    </header>

    <!-- Main content -->
    <main class="flex flex-1 overflow-hidden">
      <!-- 左側 直播畫面區 -->
      <section
        class="flex-1 flex flex-col items-center justify-center px-6 py-8"
      >
        <div
          class="relative h-full max-w-3xl rounded-2xl aspect-video flex items-center justify-center"
        >
          <!-- 預覽區（可加 logo、LIVE 標籤等） -->
          <video
            ref="videoRef"
            autoplay
            loop
            playsinline
            class="h-full object-cover rounded-2xl"
            :controls="false"
            tabindex="-1"
          ></video>
        </div>
        <div class="mt-4 text-gray-400">
          info 你尚未加入直播，點擊下方開啟鏡頭或麥克風
        </div>
      </section>
      <!-- 右側 聊天室 -->
      <aside class="w-96 min-w-[320px] bg-white/90 border-l flex flex-col">
        <div class="flex-1 p-4 overflow-y-auto">
          <div class="font-semibold text-lg mb-3">聊天室</div>
          <!-- 訊息串 ... -->
        </div>
        <div class="border-t p-3 flex items-center space-x-2">
          <input
            class="flex-1 border rounded px-3 py-2"
            placeholder="發送訊息..."
          />
          <button class="bg-blue-600 text-white px-4 py-2 rounded">發送</button>
        </div>
      </aside>
    </main>

    <footer
      class="flex flex-wrap justify-center items-center gap-3 py-5 w-full"
    >
      <button
        class="flex items-center gap-2 px-4 py-2 rounded-full bg-gray-100 text-gray-700 hover:bg-gray-200 shadow transition-all duration-150"
      >
        <span class="material-icons text-base">mic_off</span>
        <span class="hidden sm:inline">靜音</span>
      </button>
      <button
        class="flex items-center gap-2 px-4 py-2 rounded-full bg-gray-100 text-gray-700 hover:bg-gray-200 shadow transition-all duration-150"
      >
        <span class="material-icons text-base">videocam_off</span>
        <span class="hidden sm:inline">關閉鏡頭</span>
      </button>
      <button
        class="flex items-center gap-2 px-4 py-2 rounded-full bg-gray-100 text-gray-700 hover:bg-gray-200 shadow transition-all duration-150"
      >
        <span class="material-icons text-base">settings</span>
        <span class="hidden sm:inline">設定</span>
      </button>
      <button
        class="flex items-center gap-2 px-4 py-2 rounded-full bg-gray-100 text-gray-700 hover:bg-gray-200 shadow transition-all duration-150"
      >
        <span class="material-icons text-base">share</span>
        <span class="hidden sm:inline">分享</span>
      </button>
      <button
        class="flex items-center gap-2 px-4 py-2 rounded-full bg-gray-100 text-gray-700 hover:bg-gray-200 shadow transition-all duration-150"
      >
        <span class="material-icons text-base">person_add</span>
        <span class="hidden sm:inline">邀請</span>
      </button>
      <button
        class="flex items-center gap-2 px-4 py-2 rounded-full border-2 border-red-600 bg-white text-red-600 font-bold hover:bg-red-600 hover:text-white shadow transition-all duration-150"
      >
        <span class="material-icons text-base">logout</span>
        <span class="hidden sm:inline">離開</span>
      </button>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import Hls from "hls.js";

const videoRef = ref(null);
const hlsUrl = "http://140.113.164.38:8000/index.m3u8"

let hls = null;
const loading = ref(true);

onMounted(() => {
  if (Hls.isSupported()) {
    hls = new Hls();
    hls.loadSource(hlsUrl);
    hls.attachMedia(videoRef.value);
    videoRef.value.play();
    hls.on(Hls.Events.MANIFEST_PARSED, function () {
      loading.value = false;
    });
  } else if (videoRef.value.canPlayType("application/vnd.apple.mpegurl")) {
    videoRef.value.src = hlsUrl;
    videoRef.value.play();
    loading.value = false;
  }
});

onBeforeUnmount(() => {
  if (hls) {
    hls.destroy();
    hls = null;
  }
});
</script>

<style scoped>
@keyframes fade-in-down {
  from {
    opacity: 0;
    transform: translateY(-30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
@keyframes fade-in-up {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.animate-fade-in-down {
  animation: fade-in-down 0.7s cubic-bezier(0.4, 0, 0.2, 1);
}
.animate-fade-in-up {
  animation: fade-in-up 0.7s cubic-bezier(0.4, 0, 0.2, 1) 0.2s both;
}
</style>
