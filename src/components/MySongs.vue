// MySongs component
// import axios from "axios";


// create template
// create q-page-container with style as padding-top 15px
// create div tag with v-if as isLoading
// create api-spinner tag
// close div tag
// create div tag with v-else
// create h4 tag with style as margin-left -73% and text as My Songs
// create div tag with class as row
// create div tag with class as col list-title and text as Track
// create div tag with class as col list-title and text as Language
// create div tag with class as col list-title and text as Popularity
// create div tag with class as col list-title and text as Manage Favourite
// close div tag
// create div tag with v-for as song in displayedSongs and :key as song.id and class as row list-of-songs
// create div tag with class as col-1

// create img tag with :src as song.artwork and style as width 50px, height 50px, border-radius 3px
// close div tag
// create div tag with class as col song-title
// create p tag
// create strong tag with text as song.name

// close p tag
// close div tag
// create div tag with class as col song-title and text as song.language
// close div tag
// create div tag with class as col
// create div tag with class as col song-title and style as margin-left 22%
// create div tag with class as row
// create q-icon tag with name as thumb_up, color as blue, size as 20px

// close div tag

<template>
  <q-page-container style="padding-top: 15px">
    <!-- show spinner if loading is true -->
    <div v-if="isLoading">
      <api-spinner />
    </div>
    <div v-else>
      <h4 style="margin-left: -73%">My Songs</h4>
      <div class="row">
        <div class="col list-title">Track</div>
        <div class="col list-title">Language</div>
        <div class="col list-title">Popularity</div>
        <div class="col list-title">Manage Favourite</div>
      </div>
      <!-- loop through displayedSongs -->
      <div
        v-for="song in displayedSongs"
        :key="song.id"
        class="row list-of-songs"
      >
        <div class="col-1">
          <img
            :src="song.artwork"
            style="width: 50px; height: 50px; border-radius: 3px"
          />
        </div>
        <div class="col song-title">
          <p>
            <strong>{{ song.name }}</strong>
          </p>
        </div>
        <div class="col song-title">{{ song.language }}</div>
        <div class="col">
          <!-- show thumb up icon -->
          <div class="col song-title" style="margin-left: 22%">
            <div class="row">
              <q-icon name="thumb_up" color="blue" size="20px" />
            </div>
            <div class="row">{{ song.favorite_count }}</div>
          </div>
        </div>
        <div class="col">
          <!-- show like icon -->
          <q-icon
            name="favorite"
            :color="isSavedSong(song.entity_id) ? 'red' : 'grey'"
            size="20px"
            @click="handleSave(song)"
          />
        </div>
      </div>
    </div>
  </q-page-container>
</template>
<script>
// import axios, ref, onmounted
import { ref, onMounted, watch } from "vue";
import axios from "axios";
// import ApiSpinner
import ApiSpinner from "./ApiSpinner.vue";
// import getAuthHeader
import { getAuthHeader } from "../util/authenticationUtil";

export default {
  components: {
    ApiSpinner,
  },
  // searchedKeyword prop
  props: {
    searchedKeyword: {
      type: String,
      required: false,
    },
  },
  setup(props) {
    // create a reference to the songs
    const songs = ref([]);
    // displayedSongs ref
    const displayedSongs = ref([]);
    // savedSongs ref
    const savedSongs = ref([]);
    // isLoading ref
    const isLoading = ref(true);

    // function to get saved songs
    const getSavedSongs = async () => {
      const response = await axios.get(
        "http://localhost:8090/api/gaana/saved-songs", getAuthHeader()
      );
      songs.value = displayedSongs.value = response.data;
      savedSongs.value = response.data.map((song) => song.entity_id);
    };

    // function to save a song
    const saveSong = async (song) => {
      await axios.post("http://localhost:8090/api/gaana/song", song, getAuthHeader());
    };

    // function to remove a song
    const removeSong = async (songId) => {
      await axios.delete(`http://localhost:8090/api/gaana/song/${songId}`, getAuthHeader());
      await getSavedSongs();
    };

    // fetch the songs from the API
    onMounted(async () => {
      isLoading.value = true;
      await getSavedSongs();
      isLoading.value = false;
    });

    // watch for changes in the searchedKeyword
    // and filter the displayedSongs based on name
    watch(
      () => props.searchedKeyword,
      (searchedKeyword) => {
        displayedSongs.value = songs.value.filter((song) =>
          song.name.toLowerCase().includes(searchedKeyword.toLowerCase())
        );
      }
    );

    // handle the save button click
    const handleSave = async (song) => {
      if (savedSongs.value.includes(song.entity_id)) {
        await removeSong(song.entity_id);
      } else {
        await saveSong(song);
      }
      await getSavedSongs();
    };

    // is saved song
    const isSavedSong = (entity_id) => {
      return savedSongs?.value?.includes(entity_id);
    };

    return {
      displayedSongs,
      isLoading,
      handleSave,
      isSavedSong,
    };
  },
};
</script>
<style scoped>
.list-title {
  font-weight: bold;
}
.list-of-songs {
  padding-top: 2%;
  /* bottom border 1px solid */
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  border-top: 1px solid rgba(0, 0, 0, 0.1);
  padding-bottom: 1%;
}
.song-title {
  /* 1% padding top */
  padding-top: 1%;
  margin-left: 2.6%;
  text-align: left;
}
</style>
