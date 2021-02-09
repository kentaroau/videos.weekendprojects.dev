<template>
  <div class="heading">
    <h2>
      Random Videos <span>{{ videos.length - currentVideoIndex }}</span>
    </h2>
    <div class="btnContainer">
      <button v-on:click="previousVideo">Previous</button>

      <button v-on:click="nextVideo">Next</button>
    </div>
  </div>
  <div class="vidContainer">
    <iframe
      width="420"
      height="315"
      frameborder="0"
      allowfullscreen
      id="youtubeContainer"
      v-bind:src="currentVideoUrl"
    ></iframe>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      videos: [],
      currentVideoUrl: "",
      currentVideoIndex: 0,
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
                url: iframe.firstElementChild.getAttribute("src"),
              });
            }
          });

          self.currentVideoUrl = self.videos[0].url;
          console.log("found videos:" + self.videos.length);
        }
      } else {
        // What do when the request fails
        console.log("The request failed!");
      }
    };

    xhr.open("GET", "https://www.reddit.com/r/videos.json?limit=100");
    xhr.send();
  },
  methods: {
    previousVideo() {
      this.currentVideoIndex--;
      this.currentVideoUrl = this.videos[this.currentVideoIndex].url;
    },
    nextVideo() {
      this.currentVideoIndex++;
      this.currentVideoUrl = this.videos[this.currentVideoIndex].url;
    },
  },
};
</script>

<style>
#app {
  width: 50%;
}
button {
  background-color: white;
  border-radius: 4px;
  font-size: 1.6rem;
  padding: 0.5rem 1.5rem;
  margin-right: 1rem;
  width: 12rem;
  border: 3px solid black;
}
button:hover{
  transform: scaleX(1px);
}
.heading{
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
.heading h2{
  color: #acb8cc;

}


.vidContainer {
  overflow: hidden;
  padding-bottom: 56.25%;
  position: relative;
  height: 0;
  margin-top: 1rem;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.vidContainer iframe {
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  position: absolute;
  border-radius: 8px;
}
</style>