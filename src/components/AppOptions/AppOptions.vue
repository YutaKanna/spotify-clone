<template>
  <ul
    :class="{
      'top-[18.4rem] left-[18.4rem] p-[4px] ': diskografiPage && firstElement,
      'top-[11.3rem] left-[18.4rem] p-[4px] ': diskografiPage && !firstElement,
      'left-[9.2rem]': playlistPage || albumPage || TrackPage,
    }"
    class="app--option absolute z-[999] bg-dark2 p-[4px] w-fit h-fit text-opacwhite3 whitespace-normal rounded shadow-[0px_15px_15px_1px_rgba(0,0,0,0.4)]"
  >
    <li
      v-if="isPlayingCurrentSectionTrack"
      @click="addToQueue((uri = currentUri))"
      class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3 truncate"
    >
      <button class="cursor-default">
        <span class="text-xs md:text-sm">Add to queue</span>
      </button>
    </li>
    <li
      class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3 border-b border-opacwhite1"
    >
      <button class="cursor-default">
        <span v-if="TrackPage" class="text-xs md:text-sm">
          Go to track radio
        </span>
        <span
          v-if="albumPage || singlePage || diskografiPage"
          class="text-xs md:text-sm"
        >
          Go to Artist radio
        </span>
        <span v-if="playlistPage" class="text-xs md:text-sm">
          Go to playlist radio
        </span>
      </button>
    </li>
    <li
      v-if="TrackPage || playlistPage"
      class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3"
    >
      <button v-if="TrackPage" class="cursor-default">
        <span class="text-xs md:text-sm">Show credits</span>
      </button>
      <button
        v-if="playlistPage"
        class="flex items-center justify-between w-full cursor-default text-white"
      >
        <span class="text-xs md:text-sm justify-start">Report</span>
        <svg role="img" height="16" width="16" viewBox="0 0 16 16">
          <path
            fill="currentColor"
            d="M1 2.75A.75.75 0 011.75 2H7v1.5H2.5v11h10.219V9h1.5v6.25a.75.75 0 01-.75.75H1.75a.75.75 0 01-.75-.75V2.75z"
          ></path>
          <path
            fill="currentColor"
            d="M15 1v4.993a.75.75 0 11-1.5 0V3.56L8.78 8.28a.75.75 0 01-1.06-1.06l4.72-4.72h-2.433a.75.75 0 010-1.5H15z"
          ></path>
        </svg>
      </button>
    </li>
    <li
      v-if="TrackPage"
      class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3"
    >
      <button class="cursor-default">
        <span v-if="!TrackPage" class="text-xs md:text-sm">
          Save to your Liked Songs
        </span>
        <span v-else class="text-xs md:text-sm"
          >Beğenilen Şarkılar listesinden kaldır</span
        >
      </button>
    </li>
    <li
      v-if="diskografiPage"
      @click="unFollowAlbumFunc(_, $event)"
      class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3"
    >
      <button v-if="!isFavAlbum" class="cursor-default">
        <span class="text-xs md:text-sm"> Kitaplığına Ekle </span>
      </button>
      <button v-else class="cursor-default">
        <span class="text-xs md:text-sm"> Kitaplığından Kaldır </span>
      </button>
    </li>
    <li
      v-if="albumPage"
      @click="unFollowAlbum"
      class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3"
    >
      <button v-if="!isFavAlbum" class="cursor-default">
        <span class="text-xs md:text-sm">Add to Your Library</span>
      </button>
      <button v-else class="cursor-default">
        <span class="text-xs md:text-sm">Remove from Your Library</span>
      </button>
    </li>
    <li
      v-if="playlistPage"
      @click="unFollowPlaylist"
      class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3"
    >
      <button v-if="!isFavPlaylist" class="cursor-default">
        <span class="text-xs md:text-sm">Add to Your Library</span>
      </button>
      <button v-else class="cursor-default">
        <span class="text-xs md:text-sm">Remove from Your Library</span>
      </button>
    </li>

    <li
      v-if="albumPage || singlePage || TrackPage || diskografiPage"
      @mouseenter="visibleFunc"
      @mouseleave="visibleFunc2"
      class="group w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3 border-b border-opacwhite1"
    >
      <button class="cursor-default flex items-center justify-between w-full">
        <span class="text-xs md:text-sm">Add to playlist</span>
        <span class="text-white">
          <svg
            role="img"
            height="16"
            width="16"
            class="rotate-90"
            viewBox="0 0 16 16"
          >
            <path fill="currentColor" d="M14 10L8 4l-6 6h12z"></path>
          </svg>
        </span>
      </button>
      <ul
        v-if="visible"
        :class="{
          'left-[14.8rem] top-[-12.2rem]': TrackPage,
          'left-[9.6rem] top-[-12.3rem]': albumPage,
          'left-[9.5rem] top-[-15rem]': diskografiPage,
        }"
        class="z-50 absolute bg-dark2 p-[4px] h-[25rem] w-fit text-opacwhite3 whitespace-normal rounded shadow-[0px_15px_15px_1px_rgba(0,0,0,0.4)]"
      >
        <li
          class="w-full flex items-center justify-start hover:bg-dark3 border-opacwhite1 mb-1"
        >
          <input
            v-model="searchEl"
            type="text"
            placeholder="Find playlist"
            class="text-sm w-[15.3rem] bg-opacwhite relative rounded-md pl-8 h-8 left-0 focus:outline-none"
          />
          <div class="absolute pl-2">
            <span class=""
              ><svg
                role="img"
                height="16"
                width="16"
                class="text-opacwhite5"
                aria-hidden="true"
                viewBox="0 0 16 16"
              >
                <path
                  fill="currentColor"
                  d="M7 1.75a5.25 5.25 0 100 10.5 5.25 5.25 0 000-10.5zM.25 7a6.75 6.75 0 1112.096 4.12l3.184 3.185a.75.75 0 11-1.06 1.06L11.304 12.2A6.75 6.75 0 01.25 7z"
                ></path></svg
            ></span>
          </div>
          <button @click="clean" v-if="typing" class="right-0 px-3 absolute">
            <svg
              role="img"
              height="16"
              width="16"
              viewBox="0 0 16 16"
              class="text-opacwhite5"
            >
              <path
                fill="currentColor"
                d="M1.47 1.47a.75.75 0 011.06 0L8 6.94l5.47-5.47a.75.75 0 111.06 1.06L9.06 8l5.47 5.47a.75.75 0 11-1.06 1.06L8 9.06l-5.47 5.47a.75.75 0 01-1.06-1.06L6.94 8 1.47 2.53a.75.75 0 010-1.06z"
              ></path>
            </svg>
          </button>
        </li>
        <li
          class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3 border-b border-opacwhite1"
        >
          <button class="cursor-default">
            <span class="text-xs md:text-sm">Create playlist</span>
          </button>
        </li>
      </ul>
    </li>
    <li
      @mouseenter="visible2 = true"
      @mouseleave="visible2 = false"
      class="group w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3 border-b border-opacwhite1"
    >
      <button class="cursor-default flex items-center justify-between w-full">
        <span class="text-xs md:text-sm">Share</span>
        <span class="text-white">
          <svg
            role="img"
            height="16"
            width="16"
            class="rotate-90"
            viewBox="0 0 16 16"
          >
            <path fill="currentColor" d="M14 10L8 4l-6 6h12z"></path>
          </svg>
        </span>
      </button>
      <ul
        v-if="visible2"
        :class="{
          'w-[15.5rem] left-[11.4em] ': playlistPage,
          'w-[13.3rem] top-[12.6rem]  left-[14.9rem]': TrackPage,
          'w-[13.3rem] left-[9.6rem]': albumPage,
          'right-[-9.5rem] w-full  top-[10rem]': diskografiPage,
        }"
        class="z-50 absolute bg-dark2 p-[4px] h-fit text-opacwhite3 whitespace-normal rounded shadow-[0px_15px_15px_1px_rgba(0,0,0,0.4)]"
      >
        <li
          @click="copyURL((href = currentHref))"
          class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3 border-opacwhite1"
        >
          <button class="cursor-default">
            <span
              v-if="albumPage || singlePage || diskografiPage"
              class="text-xs md:text-sm"
              >Copy Album Link</span
            >
            <span v-if="TrackPage" class="text-xs md:text-sm"
              >Copy Song Link</span
            >
            <span v-if="playlistPage" class="text-xs md:text-sm"
              >Copy link to playlist</span
            >
          </button>
        </li>
        <li class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3">
          <button class="cursor-default">
            <span
              v-if="albumPage || singlePage || diskografiPage"
              class="text-xs md:text-sm"
              >Embed Album</span
            >
            <span v-if="TrackPage" class="text-xs md:text-sm">Embed track</span>
            <span v-if="playlistPage" class="text-xs md:text-sm"
              >Embed playlist</span
            >
          </button>
        </li>
      </ul>
    </li>
    <li
      v-if="playlistPage"
      class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3 border-b border-opacwhite1"
    >
      <button class="cursor-default">
        <span class="text-xs md:text-sm">About recommendations</span>
      </button>
    </li>
    <li class="w-full flex justify-start p-[6px] md:p-[8px] hover:bg-dark3">
      <button class="cursor-default">
        <span class="text-xs md:text-sm">Open in Desktop app</span>
      </button>
    </li>
  </ul>
</template>

<script>
export default {
  name: "AppOptions",
  props: [
    "appOptions",
    "albumPage",
    "singlePage",
    "TrackPage",
    "playlistPage",
    "isFavAlbum",
    "isFavPlaylist",
    "unFollowAlbum",
    "unFollowPlaylist",
    "diskografiPage",
    "firstElement",
  ],
  emits: ["toggleAppOptions", "unFollowAlbumE"],
  data() {
    return {
      visible: false,
      visible2: false,
      searching: false,
      searchEl: "",
    };
  },
  methods: {
    paylasDropDown() {
      this.paylasDropDown = true;
    },
    async addToQueue(uri, id) {
      console.log(uri);
      await this.$store.dispatch("controller/addItemToQueue", uri);
      this.$store.dispatch("controller/modalInfoType", {
        type: "queue",
        status: true,
      });
      this.close();
    },
    visibleFunc() {
      this.visible = true;
      document.removeEventListener("click", this.close);
    },
    visibleFunc2() {
      this.visible = false;
      document.addEventListener("click", this.close);
    },
    clean() {
      this.searchEl = "";
    },
    close(e) {
      if (!this.$el.contains(e?.target)) {
        if (this.appOptions) this.$emit("toggleAppOptions", false);
        console.log("clickledi", this.appOptions);
      } else return;
    },
    unFollowAlbumFunc(_, e) {
      this.$emit("unFollowAlbumE", e.target.closest(".app--option").id);
      this.$emit("toggleAppOptions");
    },

    copyURL(href) {
      navigator.clipboard.writeText(href);
      this.$store.dispatch("controller/modalInfoType", {
        type: "copyURL",
        status: true,
      });
      this.close();
    },
  },
  computed: {
    getCurrentlyPlayingTrack() {
      return this.$store.getters["controller/getCurrentlyPlayingTrack"];
    },
    currentUri() {
      return this.TrackPage
        ? this.$store.getters["albums/getCurrentTrack"]?.uri
        : this.playlistPage
        ? this.$store.getters["playlists/getPlaylist"]?.uri
        : this.albumPage
        ? this.$store.getters["albums/getAlbum"]?.uri
        : "";
    },
    currentHref() {
      return this.TrackPage
        ? this.$store.getters["albums/getCurrentTrack"]?.external_urls?.spotify
        : this.playlistPage
        ? this.$store.getters["playlists/getPlaylist"]?.external_urls?.spotify
        : this.albumPage
        ? this.$store.getters["albums/getAlbum"]?.external_urls?.spotify
        : "";
    },
    isPlayingCurrentSectionTrack() {
      return this.getCurrentlyPlayingTrack?.item.uri === this.currentUri
        ? false
        : true;
    },

    typing() {
      return this.searchEl.length
        ? (this.searching = true)
        : (this.searching = false);
    },
  },
  mounted() {
    setTimeout(() => {
      document.addEventListener("click", this.close);
    }, 100);
  },
  beforeUnmount() {
    document.removeEventListener("click", this.close);
  },
};
</script>

<style></style>
