<template>
  <v-row>
    <v-col>
      <v-textarea class="text-h4 rounded-lg" filled auto-grow label="Title" rows="1" v-model="memoTitle">
      </v-textarea>
      <v-textarea class="text-h5 rounded-lg" filled auto-grow label="Memo Text" v-model="memoText">
      </v-textarea>
      <v-btn @click="back()">もどる</v-btn>
      <v-btn @click="postMemo()">新規作成</v-btn>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: "createPage",
  data() {
    return {
      memoTitle: "",
      memoText: "",
    }
  },
  methods: {
    back() {
      this.$router.push('/');
    },
    async postMemo() {
      const db = this.$fire.firestore;
      const memoCollection = db.collection("memoData");

      try {

        const postData = {title: this.memoTitle, text: this.memoText, lastUpdate: this.$fireModule.firestore.Timestamp.now()}
        await memoCollection.add(postData);
        console.log(postData);
        await this.$router.push('/');
      } catch(e) {
        console.log(e);
      }
    }
  }
}
</script>
