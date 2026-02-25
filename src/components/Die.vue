<template>
  <div
    v-if="socketStatus == 'OPEN'"
    class="die"
    :class="colorClass"
    @click="rollDie"
  >
    <div class="face">
      <div v-for="_ in pips" class="pip"></div>
    </div>
  </div>
  <div v-else class="status">
    {{ { CONNECTING: "CONNECTING...", CLOSED: "DISCONNECTED" }[socketStatus] }}
  </div>
</template>

<script setup>
import { ref, watch, computed } from "vue";
import { useWebSocket } from "@vueuse/core";
import { useSound } from "@vueuse/sound";
import diceShaking from "../assets/dice-shaking.mp3";
import diceLanding from "../assets/dice-landing.mp3";

const { passKey } = defineProps({
  passKey: {
    type: String,
    required: true,
  },
});
const emit = defineEmits(["clearPassKey"]);

const { play: playDiceShaking, stop: stopDiceShaking } = useSound(diceShaking);
const { play: playDiceLanding } = useSound(diceLanding);

const {
  send: sendRoll,
  data: receivedRoll,
  status: socketStatus,
} = useWebSocket(
  `https://please-die-server-production.up.railway.app/${passKey}`,
  {
    autoReconnect: {
      retries: 3,
      onFailed() {
        emit("clearPassKey");
      },
    },
  },
);

const pips = ref();
const rollIntervalID = ref();

const rollDie = () => {
  const getNextFace = (currentFace) => {
    const faces = [1, 2, 3, 4, 5, 6].filter((result) => result !== currentFace);
    const randomIndex = Math.floor(Math.random() * faces.length);
    return faces[randomIndex];
  };

  const rollPayload = [];
  for (let i = 0; i < 10; i++) {
    const lastFace = rollPayload[rollPayload.length - 1] || pips.value;
    rollPayload.push(getNextFace(lastFace));
  }

  sendRoll(JSON.stringify(rollPayload));
};

watch(receivedRoll, () => {
  clearInterval(rollIntervalID.value);
  playDiceShaking();

  const rollFaces = JSON.parse(receivedRoll.value);

  rollIntervalID.value = setInterval(() => {
    if (rollFaces.length > 0) {
      pips.value = rollFaces.shift();
    } else {
      clearInterval(rollIntervalID.value);
      stopDiceShaking();
      playDiceLanding();
    }
  }, 100);
});

const colorClass = computed(() => {
  const colorMap = {
    1: "red",
    2: "magenta",
    3: "blue",
    4: "cyan",
    5: "green",
    6: "yellow",
  };

  return { [colorMap[pips.value]]: true };
});
</script>

<style scoped>
.status {
  color: var(--color-white);
  font-size: 2em;
}

.die {
  cursor: pointer;
  user-select: none;
  border: 1.5em solid var(--color-white);
  border-radius: 3em;
  height: 25em;
  width: 25em;
}

.face {
  height: 100%;
  padding: 3em;
  display: grid;
  gap: 1em;
  grid-template-columns: repeat(3, auto);
  grid-template-rows: repeat(3, auto);
  grid-template-areas:
    "a . c"
    "e g f"
    "d . b";
}

.pip {
  border-radius: 50%;
}

.pip:nth-child(2) {
  grid-area: b;
}

.pip:nth-child(3) {
  grid-area: c;
}

.pip:nth-child(4) {
  grid-area: d;
}

.pip:nth-child(5) {
  grid-area: e;
}

.pip:nth-child(6) {
  grid-area: f;
}

.pip:nth-child(odd):last-child {
  grid-area: g;
}

.red {
  border-color: var(--color-red);

  .pip {
    background-color: var(--color-red);
  }
}

.yellow {
  border-color: var(--color-yellow);

  .pip {
    background-color: var(--color-yellow);
  }
}

.green {
  border-color: var(--color-green);

  .pip {
    background-color: var(--color-green);
  }
}

.cyan {
  border-color: var(--color-cyan);

  .pip {
    background-color: var(--color-cyan);
  }
}

.blue {
  border-color: var(--color-blue);

  .pip {
    background-color: var(--color-blue);
  }
}

.magenta {
  border-color: var(--color-magenta);

  .pip {
    background-color: var(--color-magenta);
  }
}
</style>
