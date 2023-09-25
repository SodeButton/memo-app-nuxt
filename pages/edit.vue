<template>
  <v-container style="max-width: 600px" class="mt-2">
    <v-row>
      <v-col cols="6">
        <v-btn @click="back()">もどる</v-btn>
      </v-col>
      <v-col cols="6" class="text-right">
        <v-btn @click="saveMemo()" :disabled="memoText.length === 0 || isLoading">上書き保存</v-btn>
      </v-col>
      <v-col cols="12">
        <v-textarea
          solo
          hide-details
          rows="20"
          no-resize
          placeholder="メモを作成する..."
          :loading="isLoading"
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
      id: null,
      isLoading: true,
      isNoId: false,
    }
  },
  created() {
    this.id = this.$route.query.id;
    if (this.id === undefined) {
      this.isNoId = true;
      return;
    }
    const db = this.$fire.firestore;
    const memoCollection = db.collection("memoData");
    try {
      memoCollection.doc(this.id).onSnapshot(snapshot => {
        if(snapshot.data() === undefined) {
          this.isNoId = true;
          return;
        }
        this.memoText = snapshot.data().text;
        this.isLoading = false;
      });
    } catch (e) {
      console.log(e);
    }
  },
  mounted() {
  },
  methods: {
    back() {
      this.$router.push('/');
    },
    async saveMemo() {
      if (this.memoText === "") {
        return;
      }

      const db = this.$fire.firestore;
      const memoCollection = db.collection("memoData");
      const saveData = {text: this.memoText, lastUpdate: this.$fireModule.firestore.Timestamp.now()}

      try {
        await memoCollection.doc(this.id).update(saveData);
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
