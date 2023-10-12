<template>
<img class="posterLink" :src="urlImage" />
</template>


<script>
import { useFileDialog } from "@vueuse/core";
import { ref as storageRef } from "firebase/storage";
import { useFirebaseStorage, useStorageFile,useStorageFileUrl  } from "vuefire";

export default {
  props: {
    linkImg: {
        type: String,
        required: true
    }
  },
  // setup(props) {
  //   let me =this;
  //   const storage = useFirebaseStorage();
  //   const mountainFileRef = storageRef(storage, props.linkImg);
  //   const { url, refresh } = useStorageFileUrl(mountainFileRef);
  //   this.urlImage = useStorageFileUrl(mountainFileRef).url ? useStorageFileUrl(mountainFileRef).url : "";
  //   return {
  //     url,refresh
  //   };
  // },
  created() {
    let me =this;
    const storage = useFirebaseStorage();
    const mountainFileRef = storageRef(storage, this.linkImg);
    const { url, refresh } = useStorageFileUrl(mountainFileRef);
    this.urlImage = useStorageFileUrl(mountainFileRef).url ? useStorageFileUrl(mountainFileRef).url : "";
  },

  watch: {
    linkImg(newValue, oldValue) {
      let me =this;
      const storage = useFirebaseStorage();
      const mountainFileRef = storageRef(storage, this.linkImg);
      const { url, refresh } = useStorageFileUrl(mountainFileRef);
      this.urlImage = useStorageFileUrl(mountainFileRef).url ? useStorageFileUrl(mountainFileRef).url : "";
    },
  },
  data() {
    return {
     urlImage: ''
    };
  },

};
</script>

<style lang="css">
</style>