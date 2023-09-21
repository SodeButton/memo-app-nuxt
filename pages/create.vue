<template>
  <v-container style="max-width: 600px" class="mt-2">
    <v-row>
      <v-col cols="6">
        <v-btn @click="back()">もどる</v-btn>
      </v-col>
      <v-col cols="6" class="text-right">
        <v-btn @click="postMemo()" :disabled="memoText.length === 0">新規作成</v-btn>
      </v-col>
      <v-col cols="12">
        <v-textarea
          class="rounded-lg"
          solo
          hide-details
          rows="20"
          no-resize
          placeholder="メモを作成する..."
          v-model="memoText"
        >
        </v-textarea>
      </v-col>
    </v-row>
  </v-container>
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
  mounted() {
  },
  methods: {
    back() {
      this.$router.push('/');
    },
    async postMemo() {

      if (this.memoText === "") {
        return;
      }

      const db = this.$fire.firestore;
      const memoCollection = db.collection("memoData");

      try {
        const postData = {text: this.memoText, lastUpdate: this.$fireModule.firestore.Timestamp.now()}
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
<style>
::-webkit-scrollbar{
  width: 10px;
}
::-webkit-scrollbar-track{
  background: #fff;
  background: none;
  border: none;
  border-radius: 10px;
  box-shadow: none;
}
::-webkit-scrollbar-thumb{
  background: #888;
  border-radius: 10px;
  box-shadow: none;
}
</style>
