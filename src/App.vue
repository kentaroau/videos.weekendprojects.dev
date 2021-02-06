<template>
  <h2>Hello test</h2>

  <div class="vidContainer">

    <iframe id="youtubeContainer" v-bind:src="currentVideoUrl"></iframe>

  </div>
  <button v-on:click="prevVideo">Previous</button>

  <button v-on:click="nextVideo">Next</button>


</template>

<script>
export default {
  name: "App",
  data() {
    return {
      videos: [],
      currentVideoUrl: "",
      currentVideoIndex:0
    };
  },
  mounted() {
    // get data
    var xhr = new XMLHttpRequest();
    var self = this;
    // Setup our listener to process completed requests
    xhr.onload = function () {
      // Process our return data
      if (xhr.status >= 200 && xhr.status < 300) {
        if (xhr) {
          let response = JSON.parse(xhr.response);

          response.data.children.forEach((element) => {
            if (
              element.data.media &&
              element.data.media.type == "youtube.com"
            ) {
              console.log(element);

              const container = document
                .createRange()
                .createContextualFragment(element.data.media.oembed.html);

              const iframe = document
                .createRange()
                .createContextualFragment(container.firstChild.data);

              self.videos.push({
                name: "xxx",
                url: iframe.firstElementChild.getAttribute("src")
              });
            }
          });

          self.currentVideoUrl = self.videos[0].url;
          console.log("found videos:" + self.videos.length)
        }
      } else {
        // What do when the request fails
        console.log("The request failed!");
      }
    };

    xhr.open("GET", "https://www.reddit.com/r/videos.json");
    xhr.send();
  },
  methods: {
    previousVideo(){
      this.currentVideoIndex--
      this.currentVideoUrl =  this.videos[this.currentVideoIndex].url;

    },
    nextVideo() {
      this.currentVideoIndex++;
      this.currentVideoUrl =  this.videos[this.currentVideoIndex].url;
    },
  },
};
</script>

<style>
#app {
}

#youtubeContainer{
  width: 1000px;
  height: 600px;

}

</style>