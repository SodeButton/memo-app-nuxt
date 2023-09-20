<template>
  <v-row>
    <div>
      <h1>メモアプリ</h1>
    </div>
    <v-btn @click="create()">新規作成</v-btn>
    <v-col v-for="(item, index) in memoData" :v-key="index" cols="12">
      <v-card>
        <v-card-title>
          {{item.title}}
        </v-card-title>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      memoData: [],
      unsubscribe: null,
    }
  },
  methods: {
    create() {
      this.$router.push('/create');
    },
    async getMemoData() {
      const db = this.$fire.firestore;
      const memoCollection = db.collection("memoData");

      try {
        const res = await memoCollection.get();
        const items = [];

        res.forEach((doc) => {
          items.push(doc.data());
        });

        return { items };
      } catch(e) {
        console.log(e);
        return { items: [] };
      }
    }
  },
  mounted() {
    const db = this.$fire.firestore;
    const memoCollection = db.collection("memoData");

    this.unsubscribe = memoCollection.onSnapshot((snapshot) => {
      const items = [];

      snapshot.forEach((doc) => {
        items.push(doc.data());
      });

      this.memoData = items;
    });
  },
  beforeDestroy() {
    if (this.unsubscribe) {
      this.unsubscribe();
    }
  }
}
</script>
