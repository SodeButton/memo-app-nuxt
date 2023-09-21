<template>
  <v-container style="max-width: 600px" class="mt-2">
    <v-row>
      <v-col cols="6">
        <h2>メモアプリ</h2>
      </v-col>
      <v-col cols="6" class="text-right">
        <v-btn @click="create()" >新しいメモ</v-btn>
      </v-col>
      <v-col cols="12">
        <v-text-field
          v-model="search"
          label="検索..."
          placeholder="検索..."
          single-line
          hide-details
          solo
          append-icon="mdi-magnify"
        ></v-text-field>
      </v-col>
      <v-col cols="12" class="text-center" v-if="loading">
        <v-progress-circular
          indeterminate
          size="50"
          width="5"
        ></v-progress-circular>
      </v-col>
      <v-col cols="12" class="text-center" v-if="loading">
        <p class="grey--text ma-auto mt-10" style="max-width: 280px">上の新しいメモボタンをクリックしてメモを作成します</p>
      </v-col>
      <v-col v-for="(item, index) in memoData" :v-key="index" cols="12" v-if="!loading">
        <v-card>
          <v-card-text>
            {{item.text}}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      memoData: [],
      unsubscribe: null,
      loading: true,
      search: '',
    }
  },
  methods: {
    create() {
      this.$router.push('/create');
    },
    async getMemoData() {
      const db = this.$fire.firestore;
      const memoCollection = db.collection('memoData');

      try {
        const res = await memoCollection.orderBy('lastUpdate', 'desc').get();
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

    this.unsubscribe = memoCollection.orderBy("lastUpdate", "desc").onSnapshot((snapshot) => {
      const items = [];

      snapshot.forEach((doc) => {
        items.push(doc.data());
      });

      this.memoData = items;
    });
    this.loading = false;
  },
  beforeDestroy() {
    if (this.unsubscribe) {
      this.unsubscribe();
    }
  }
}
</script>
