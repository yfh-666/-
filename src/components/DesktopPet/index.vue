<template>
  <div
    class="desktop-pet"
    :style="petStyle"
    :class="{ dragging, 'pet-jump': isJumping }"
    v-if="petVisible"
    @mousedown="startDrag"
    @touchstart="startDrag"
    @click="showMenu"
    @dblclick="happy"
    @contextmenu.prevent="interact"
  >
    <img :src="petImages[mood]" alt="桌宠" />
    <transition name="fade">
      <div v-if="showMsg" class="pet-msg">{{ currentMsg }}</div>
    </transition>
    <ul v-if="menuVisible" class="pet-menu" :style="menuStyle" @mousedown.stop>
      <li @click="onHidePet">隐藏桌宠</li>
      <li @click="onChangeMood">切换表情</li>
      <li @click="onAngry">让我生气</li>
      <li @click="goAiTalk">AI对话</li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();

const petImages = {
  normal: require("@/assets/images/pet.png"),
  happy: require("@/assets/images/pet_happy.png"),
  angry: require("@/assets/images/pet_angry.png")
};

const moods = ["normal", "happy", "angry"] as const;
type Mood = (typeof moods)[number];

const pos = ref({ x: window.innerWidth - 120, y: window.innerHeight - 180 });
const dragging = ref(false);
let offset = { x: 0, y: 0 };

const isJumping = ref(false);
const mood = ref<Mood>("normal");
const petVisible = ref(true);

const goAiTalk = () => {
  menuVisible.value = false;
  router.push("/user/AiTalk");
};

// 拖拽相关
const startDrag = (e: MouseEvent | TouchEvent) => {
  dragging.value = true;
  const clientX = "touches" in e ? e.touches[0].clientX : e.clientX;
  const clientY = "touches" in e ? e.touches[0].clientY : e.clientY;
  offset.x = clientX - pos.value.x;
  offset.y = clientY - pos.value.y;
  document.addEventListener("mousemove", onDrag);
  document.addEventListener("mouseup", stopDrag);
  document.addEventListener("touchmove", onDrag);
  document.addEventListener("touchend", stopDrag);
};
const onDrag = (e: MouseEvent | TouchEvent) => {
  if (!dragging.value) return;
  const clientX = "touches" in e ? e.touches[0].clientX : e.clientX;
  const clientY = "touches" in e ? e.touches[0].clientY : e.clientY;
  pos.value.x = Math.max(
    0,
    Math.min(clientX - offset.x, window.innerWidth - 100)
  );
  pos.value.y = Math.max(
    0,
    Math.min(clientY - offset.y, window.innerHeight - 100)
  );
};
const stopDrag = () => {
  dragging.value = false;
  document.removeEventListener("mousemove", onDrag);
  document.removeEventListener("mouseup", stopDrag);
  document.removeEventListener("touchmove", onDrag);
  document.removeEventListener("touchend", stopDrag);
};

// 消息队列
const msgQueue = ref<string[]>([]);
const showMsg = ref(false);
const currentMsg = ref("");
let msgTimer: number | null = null;

function showMessage(message: string, duration = 1500) {
  msgQueue.value.push(message);
  if (!showMsg.value) {
    nextMsg(duration);
  }
}
function nextMsg(duration = 1500) {
  if (msgQueue.value.length === 0) {
    showMsg.value = false;
    return;
  }
  currentMsg.value = msgQueue.value.shift()!;
  showMsg.value = true;
  if (msgTimer) clearTimeout(msgTimer);
  msgTimer = window.setTimeout(() => {
    showMsg.value = false;
    nextMsg(duration);
  }, duration);
}

// 互动相关
const interact = () => {
  isJumping.value = true;
  showMessage("你戳我干嘛呀~", 1000);
  mood.value = "normal";
  setTimeout(() => {
    isJumping.value = false;
  }, 1000);
};
const happy = () => {
  mood.value = "happy";
  showMessage("好开心呀！", 1200);
  setTimeout(() => {
    mood.value = "happy";
  }, 1200);
};
const angry = () => {
  mood.value = "angry";
  showMessage("哼！我生气了！", 1200);
};

// 随机说话
const petWords = [
  "今天也要加油哦！",
  "记得多喝水~",
  "遇到问题可以问我！",
  "休息一下眼睛吧~",
  "你可以右键我试试~"
];
onMounted(() => {
  window.addEventListener("resize", () => {
    pos.value.x = Math.min(pos.value.x, window.innerWidth - 100);
    pos.value.y = Math.min(pos.value.y, window.innerHeight - 100);
  });
  setInterval(() => {
    showMessage(petWords[Math.floor(Math.random() * petWords.length)], 2000);
  }, 10000);
});

// 右键菜单
const menuVisible = ref(false);
const showMenu = () => {
  menuVisible.value = true;
  setTimeout(() => {
    document.addEventListener("mousedown", closeMenu, { once: true });
  });
};
const closeMenu = () => {
  menuVisible.value = false;
};

// 菜单按钮事件
const onHidePet = () => {
  menuVisible.value = false;
  petVisible.value = false;
};
const onChangeMood = () => {
  const idx = moods.indexOf(mood.value);
  mood.value = moods[(idx + 1) % moods.length];
  showMessage(
    `现在是${mood.value === "normal" ? "普通" : mood.value}心情~`,
    1200
  );
  menuVisible.value = false;
};
const onAngry = () => {
  menuVisible.value = false;
  angry();
};

// 样式计算
const petStyle = computed(() => ({
  left: pos.value.x + "px",
  top: pos.value.y + "px",
  opacity: dragging.value ? 0.7 : 1,
  cursor: dragging.value ? "grabbing" : "pointer",
  zIndex: 9999
}));
const menuStyle = computed(() => ({
  left: pos.value.x + "px",
  top: pos.value.y + 100 + "px", // 菜单显示在桌宠下方
  zIndex: 10000
}));
</script>

<style scoped>
.desktop-pet {
  position: fixed;
  width: 100px;
  height: 100px;
  user-select: none;
  transition: box-shadow 0.2s, opacity 0.2s;
}
.desktop-pet.dragging {
  box-shadow: 0 0 16px #aaa;
}
.desktop-pet img {
  width: 100%;
  height: 100%;
  pointer-events: none;
}
.pet-jump {
  animation: jump 0.5s;
}
@keyframes jump {
  0% {
    transform: translateY(0);
  }
  30% {
    transform: translateY(-30px);
  }
  60% {
    transform: translateY(0);
  }
  100% {
    transform: translateY(0);
  }
}
.pet-msg {
  position: absolute;
  left: 50%;
  top: -36px;
  transform: translateX(-50%);
  background: #fffbe8;
  color: #333;
  border-radius: 16px;
  padding: 6px 16px;
  font-size: 14px;
  box-shadow: 0 2px 8px #eee;
  white-space: nowrap;
  pointer-events: none;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
.pet-menu {
  position: fixed;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px #aaa;
  padding: 0;
  margin: 0;
  list-style: none;
  width: 120px;
  z-index: 10000;
}
.pet-menu li {
  padding: 10px 16px;
  cursor: pointer;
  font-size: 15px;
  transition: background 0.2s;
}
.pet-menu li:hover {
  background: #f5f5f5;
}
</style>
