<template>
  <section class="chat-view">
    <aside class="chat-inbox">
      <button
        v-for="item in inbox"
        :key="`inbox-card-${item.id}`"
        class="inbox-card"
        @click="onSelectChat(item.id)"
      >
        <h3>{{ item.name }}</h3>
        <p>{{ item.lastMessage }}</p>
        <span>{{ item.lastMessageTime.toLocaleTimeString() }}</span>
      </button>
    </aside>
    <Transition mode="out-in" @enter="onChatThreadEnter">
      <article v-if="selectedChatId" :key="selectedChatId" class="chat-thread" ref="chat-thread">
        <div v-for="message in getSelectedChatMessages()" :key="message.id" class="chat-bubble">
          <div class="chat-bubble__bubble" :class="getBubbleClass(message.sender)">
            <p>{{ message.content }}</p>
            <span>{{ message.timestamp.toLocaleTimeString() }}</span>
          </div>
        </div>
      </article>
    </Transition>
  </section>
</template>

<script setup lang="ts">
import { nextTick, ref, type Ref, useTemplateRef, watch } from "vue";
import { useScroll } from "@vueuse/core";

type Inbox = {
  id: number;
  name: string;
  lastMessage: string;
  lastMessageTime: Date;
  messages: Message[];
};

type Message = {
  id: number;
  content: string;
  sender: "customer" | "agent";
  timestamp: Date;
};

const chatThread = useTemplateRef("chat-thread");
const { y, isScrolling, arrivedState, directions } = useScroll(chatThread, {
  behavior: "smooth",
});

watch([y, isScrolling, arrivedState, directions], (value) => {
  console.log("Scroll state changed:", value);
});

const inbox: Ref<Inbox[]> = ref([
  {
    id: 1,
    name: "Chat 1",
    lastMessage: "Last message preview...",
    lastMessageTime: new Date(),
    messages: [
      { id: 1, content: "Hello, how can I help you?", sender: "agent", timestamp: new Date() },
      {
        id: 2,
        content: "I have an issue with my order.",
        sender: "customer",
        timestamp: new Date(),
      },
      {
        id: 3,
        content: "I'm sorry to hear that. Can you provide your order number?",
        sender: "agent",
        timestamp: new Date(),
      },
      { id: 4, content: "Sure, it's 12345.", sender: "customer", timestamp: new Date() },
      {
        id: 5,
        content: "Thank you. Let me check that for you.",
        sender: "agent",
        timestamp: new Date(),
      },
      {
        id: 6,
        content: "I see the issue. We will resolve it shortly.",
        sender: "agent",
        timestamp: new Date(),
      },
      { id: 7, content: "Thank you for your help!", sender: "customer", timestamp: new Date() },
      {
        id: 8,
        content: "You're welcome! If you have any more questions, feel free to ask.",
        sender: "agent",
        timestamp: new Date(),
      },
      { id: 9, content: "Will do. Have a great day!", sender: "customer", timestamp: new Date() },
      { id: 10, content: "You too! Take care.", sender: "agent", timestamp: new Date() },
      { id: 11, content: "Thanks again!", sender: "customer", timestamp: new Date() },
      { id: 12, content: "No problem! Goodbye!", sender: "agent", timestamp: new Date() },
      { id: 13, content: "Goodbye!", sender: "customer", timestamp: new Date() },
    ],
  },
  {
    id: 2,
    name: "Chat 2",
    lastMessage: "Last message preview...",
    lastMessageTime: new Date(),
    messages: [
      { id: 1, content: "Hi there!", sender: "customer", timestamp: new Date() },
      {
        id: 2,
        content: "Welcome! How can I assist you today?",
        sender: "agent",
        timestamp: new Date(),
      },
      {
        id: 3,
        content: "I need information about your services.",
        sender: "customer",
        timestamp: new Date(),
      },
      {
        id: 4,
        content: "Of course! We offer a variety of services. What would you like to know?",
        sender: "agent",
        timestamp: new Date(),
      },
      {
        id: 5,
        content: "I'm interested in web development.",
        sender: "customer",
        timestamp: new Date(),
      },
      {
        id: 6,
        content: "Great choice! We have experienced developers ready to help.",
        sender: "agent",
        timestamp: new Date(),
      },
      { id: 7, content: "How much does it cost?", sender: "customer", timestamp: new Date() },
      {
        id: 8,
        content: "It depends on the project scope. Can you describe what you need?",
        sender: "agent",
        timestamp: new Date(),
      },
      {
        id: 9,
        content: "I need a simple e-commerce website.",
        sender: "customer",
        timestamp: new Date(),
      },
      {
        id: 10,
        content: "For a basic e-commerce site, we'd start at around $2000.",
        sender: "agent",
        timestamp: new Date(),
      },
      {
        id: 11,
        content: "That sounds reasonable. Can you send me a quote?",
        sender: "customer",
        timestamp: new Date(),
      },
      {
        id: 12,
        content: "Absolutely! I'll email you the details shortly.",
        sender: "agent",
        timestamp: new Date(),
      },
      {
        id: 13,
        content: "Thank you! I'll review and get back to you.",
        sender: "customer",
        timestamp: new Date(),
      },
    ],
  },
  {
    id: 3,
    name: "Chat 3",
    lastMessage: "Last message preview...",
    lastMessageTime: new Date(),
    messages: [
      { id: 1, content: "Is anyone there?", sender: "customer", timestamp: new Date() },
      { id: 2, content: "Yes, I'm here! How can I help?", sender: "agent", timestamp: new Date() },
      {
        id: 3,
        content: "I want to cancel my subscription.",
        sender: "customer",
        timestamp: new Date(),
      },
      {
        id: 4,
        content: "I understand. Let me process that for you.",
        sender: "agent",
        timestamp: new Date(),
      },
      { id: 5, content: "Thank you.", sender: "customer", timestamp: new Date() },
      {
        id: 6,
        content: "Your subscription has been cancelled. Is there anything else?",
        sender: "agent",
        timestamp: new Date(),
      },
      {
        id: 7,
        content: "Yes, can I get a refund for this month?",
        sender: "customer",
        timestamp: new Date(),
      },
      {
        id: 8,
        content: "I'll check if you're eligible for a prorated refund.",
        sender: "agent",
        timestamp: new Date(),
      },
      { id: 9, content: "Okay, thank you.", sender: "customer", timestamp: new Date() },
      {
        id: 10,
        content: "Good news! You qualify for a partial refund. It will be processed in 3-5 days.",
        sender: "agent",
        timestamp: new Date(),
      },
      {
        id: 11,
        content: "Great! I appreciate your help.",
        sender: "customer",
        timestamp: new Date(),
      },
      {
        id: 12,
        content: "You're welcome! If you ever need our services again, feel free to reach out.",
        sender: "agent",
        timestamp: new Date(),
      },
      { id: 13, content: "I will. Have a good day!", sender: "customer", timestamp: new Date() },
      { id: 14, content: "You too! Take care!", sender: "agent", timestamp: new Date() },
    ],
  },
]);

const selectedChatId: Ref<number | null> = ref(null);

const getBubbleClass = (sender: "customer" | "agent") => {
  return sender === "customer" ? "chat-bubble__bubble--customer" : "chat-bubble__bubble--agent";
};

const getSelectedChatMessages = () => {
  const chat = inbox.value.find((item) => item.id === selectedChatId.value);
  return chat ? chat.messages : [];
};

const onSelectChat = (chatId: number) => {
  selectedChatId.value = chatId;
};

const onChatThreadEnter = async (el: Element) => {
  await nextTick();
  const scrollHeight = el.scrollHeight;
  const clientHeight = el.clientHeight;
  console.log("Chat thread entered:", el, { scrollHeight, clientHeight });
  y.value = scrollHeight - clientHeight;
};
</script>

<style scoped>
.chat-view {
  background: palevioletred;
  width: 60vw;
  height: 100vh;
  overflow: auto;
  display: flex;
}

.chat-inbox {
  background: beige;
  width: 240px;
  height: 100%;
  overflow: auto;
}

.chat-thread {
  background: lightcoral;
  width: calc(100% - 240px);
  height: 100%;
  overflow: auto;

  color: black;
}

.chat-bubble {
  background-color: gray;
  width: 100%;
  margin: 0.5rem 0;
  padding: 0.5rem;
  box-sizing: border-box;
  position: relative;
  display: flex;
  flex-direction: column;

  & .chat-bubble__bubble {
    max-width: 60%;
    border-radius: 8px;
    padding: 12px;

    &.chat-bubble__bubble--customer {
      background-color: lightblue;
      align-self: flex-start;
    }

    &.chat-bubble__bubble--agent {
      background-color: lightgreen;
      align-self: flex-end;
    }
  }
}

.inbox-card {
  background: white;
  width: 100%;
  padding: 1rem;
  border-bottom: 1px solid #ccc;
  text-align: left;
  outline: 0;
}

.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
