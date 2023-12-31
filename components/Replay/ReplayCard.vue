<template>
  <div>
    <v-card
      class="stream-card"
      :to="link"
      no-prefetch
      color="accentwave"
    >
      <div class="replay-thumbnail">
        <!-- Thumbnail -->
        <v-img
          :src="thumbnail || getRandomThumbnail( thumbnails )"
          :key="thumbnail || id"
          lazy-src="https://cdn.bitwave.tv/static/img/Bitwave_Banner.jpg"
          :aspect-ratio="16/9"
          :class="{ 'blur': blur }"
        />

        <!-- View Counter -->
        <!--<div class="view-counter">
          <v-chip
            color="grey darken-4"
            small
            label
          >
            <div v-if="views" class="d-flex align-center">
              <div class="white&#45;&#45;text">{{ views < 1 ? '🌊' : views }}</div>
              <v-icon v-show="views > 0" color="red" class="ml-2" small>visibility</v-icon>
            </div>
            <div v-else class="d-flex align-center">
              <div class="grey&#45;&#45;text mr-2">REPLAY</div>
              <v-icon small color="grey">visibility_off</v-icon>
            </div>
          </v-chip>
        </div>-->

        <!-- Duration Timecode -->
        <div class="replay-timecode">
          <v-chip
            color="grey darken-4"
            small
            label
          >
            <div
              v-if="duration"
              class="d-flex align-center"
            >
              <div class="white--text">{{ duration }}</div>
            </div>
          </v-chip>
        </div>

        <!-- Time Ago -->
        <div
          class="replay-timeago"
          :title="timestamp"
        >
          <v-chip
            color="grey darken-4"
            small
            label
          >
            <div
              v-if="timeAgo"
              class="d-flex align-center"
            >
              <div class="white--text">{{ timeAgo }}</div>
            </div>
          </v-chip>
        </div>
      </div>

      <!-- Replay Data -->
      <div
        class="pa-2"
      >
        <!-- Replay Title -->
        <div class="d-flex align-center">
          <div
            class="body-1 font-weight-bold text-truncate text-no-wrap primary---text mb-0"
            :title="title"
          >{{ title }}</div>

          <template v-if="views">
            <v-spacer/>
            <div
              class="d-flex align-center caption ml-2"
              :title="`${views} ${views === 1 ? 'view' : 'views'}`"
            >
              <div class="grey--text">{{ views < 1 ? '🌊' : views }}</div>
              <v-icon color="grey" small class="ml-1">visibility</v-icon>
            </div>
          </template>
        </div>

        <div class="caption d-flex align-center">

          <!-- Streamer Username -->
          <div class="d-block text-truncate grey--text">{{ username }}</div>

          <!-- NSFW Indicator -->
          <template v-if="nsfw">
            <v-divider vertical class="mx-2"/>
            <div class="d-flex align-center">
              <div class="red--text font-weight-bold">NSFQ</div>
            </div>
          </template>

          <!-- Comment Count -->
          <template>
            <v-spacer/>
            <div
              class="grey--text"
              :title="`${commentCount} ${commentCount === 1 ? 'comment' : 'comments'}`"
            >
              {{ commentCount }}
              <v-icon small color="grey">message</v-icon>
            </div>
          </template>

        </div>
      </div>
    </v-card>
  </div>
</template>

<script>
  export default {
    name: 'ReplayCard',

    props: {
      id           : { type: String },
      link         : { type: String },
      duration     : { type: String },
      thumbnail    : { type: String },
      thumbnails   : { type: Array },
      nsfw         : { type: Boolean },
      title        : { type: String },
      username     : { type: String },
      commentCount : { type: Number, default: 0 },
      views        : { type: Number, default: 0 },
      timestamp    : { type: Date },
      timeAgo      : { type: String },
      blur         : { type: Boolean, default: true },
    },

    data () {
      return {}
    },

    computed: {},

    methods: {
      getRandomThumbnail ( thumbnails ) {
        if ( !thumbnails || !thumbnails.length ) return 'https://cdn.bitwave.tv/static/img/Bitwave_Banner.jpg';
        return thumbnails[ Math.floor( Math.random() * thumbnails.length ) ];
      },
    },
  }
</script>

<style lang="scss">
  .blur > div.v-image__image {
    filter: blur(15px);
    -webkit-filter: blur(15px);
  }

  .replay-thumbnail {
    position: relative;
    background-color: black;

    .view-counter {
      position: absolute;
      top: 10px;
      right: 10px;
      opacity: .85;
      transition: .3s;
    }

    .replay-timecode {
      position: absolute;
      bottom: 0;
      left: 0;
      opacity: .75;
      transition: .3s;
    }

    .replay-timeago {
      position: absolute;
      bottom: 0;
      right: 0;
      opacity: .75;
      transition: .3s;
    }

    &:hover {
      .view-counter,
      .replay-timecode,
      .replay-timeago, {
        opacity: 1;
      }
    }
  }

  .v-card--reveal {
    align-items: center;
    bottom: 0;
    justify-content: center;
    opacity: .5;
    position: absolute;
    width: 100%;
  }
</style>
