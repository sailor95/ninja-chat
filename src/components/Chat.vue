<template>
  <div class="chat container">
    <h2 class="center teal-text">Dave Chat</h2>
    <div class="card">
      <div class="card-content">
        <ul class="messages" v-chat-scroll>
          <li v-for="message in messages" :key="message.id">
            <span class="teal-text">{{ message.name }}</span>
            <span class="grey-text text-darken-3">{{ message.content }}</span>
            <span class="grey-text time">{{ message.ts }}</span>
          </li>
        </ul>
      </div>
      <div class="card-action">
        <NewMessage :name="name" />
      </div>
    </div>
  </div>
</template>

<script>
import NewMessage from '@/components/NewMessage';
import db from '@/firebase/init';
import moment from 'moment';

export default {
  name: 'Chat',
  props: ['name'],
  components: {
    NewMessage,
  },
  data() {
    return {
      messages: [],
    };
  },
  created() {
    let ref = db.collection('messages').orderBy('ts');

    ref.onSnapshot(snap => {
      snap.docChanges().forEach(change => {
        if (change.type === 'added') {
          const { doc } = change;
          const { id } = doc;
          const { name, content, ts } = doc.data();

          this.messages.push({
            id,
            name,
            content,
            ts: moment(ts).format('lll'),
          });
        }
      });
    });
  },
};
</script>

<style>
.chat h2 {
  font-size: 2.6em;
  margin-bottom: 40px;
}
.chat span {
  font-size: 1.4em;
}
.chat .time {
  display: block;
  font-size: 0.8em;
}
.messages {
  max-height: 300px;
  overflow: auto;
}
.messages::-webkit-scrollbar {
  widows: 3px;
}
.messages::-webkit-scrollbar-track {
  background-attachment: #ddd;
}
.messages::-webkit-scrollbar-thumb {
  background: #aaa;
}
</style>
