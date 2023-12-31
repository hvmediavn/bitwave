<template>
  <div>
    <div class="gradient-background">
      <v-container fluid>

        <!-- Site Banner -->
        <message-of-the-day />

        <v-row class="justify-center">
          <v-col
            cols="12"
            md="10"
            xl="8"
          >
            <!-- Banner Stream -->
            <banner-video
              :src="live[0].src"
              :type="live[0].type"
              :poster="poster"
              :name="live[0].name"
              :mobile="mobile"
              :offline="offline"
            />
          </v-col>
        </v-row>


        <!-- Live Now Header -->
        <div class="mb-4">
          <div class="d-flex justify-space-between align-end">
            <div class="headline font-weight-light grey--text">Live Now</div>
            <v-switch
              v-model="blurNSFW"
              label="Blur NSFQ thumbnails"
              color="primary"
              hide-details
              dense
              inset
            />
          </div>

          <!-- Livestream Grid -->
          <stream-grid
            :streamers="streamers"
            :blur-nsfw="blurNSFW"
            :cols="12"
            :sm="6"
            :md="4"
            :lg="3"
            :xl="2"
          />
        </div>

        <!-- Trending Replay Header -->
        <div class="mb-4">
          <div class="d-flex justify-space-between align-end">
            <div class="headline font-weight-light grey--text">Trending Replays</div>
            <v-switch
              v-model="blurNSFW"
              label="Blur NSFQ thumbnails"
              color="primary"
              hide-details
              dense
              inset
            />
          </div>

          <!-- Display a grid of trending replays -->
          <v-lazy
            :min-height="200"
          >
            <trending-replays
              :limit="6"
              :blur-nsfw="blurNSFW"
              :cols="12"
              :sm="6"
              :md="4"
              :lg="3"
              :xl="2"
            />
          </v-lazy>
        </div>

        <!-- Recent Replay Header -->
        <div class="mb-4">
          <div class="d-flex justify-space-between align-end">
            <div class="headline font-weight-light grey--text">Recent Replays</div>
            <v-switch
              v-model="blurNSFW"
              label="Blur NSFQ thumbnails"
              color="primary"
              hide-details
              dense
              inset
            />
          </div>

          <!-- Replay Grid -->
          <v-lazy
            :min-height="200"
          >
            <replay-grid
              :limit="6"
              :blur-nsfw="blurNSFW"
              :cols="12"
              :sm="6"
              :md="4"
              :lg="3"
              :xl="2"
            />
          </v-lazy>
        </div>

        <!-- Homepage Content -->
        <about-us class="mb-5 mt-2" />

      </v-container>
    </div>

    <!-- Footer -->
    <simple-footer />
  </div>
</template>

<script>
  import StreamGrid from '@/components/StreamGrid'
  import BannerVideo from '@/components/Homepage/BannerVideo';
  import MessageOfTheDay from '@/components/Homepage/MessageOfTheDay';
  import SimpleFooter from '@/components/SubLayout/SimpleFooter';
  import AboutUs from '@/components/Homepage/AboutUs';

  const TrendingReplays = async () => await import ( '@/components/Replay/TrendingReplays' );
  const ReplayGrid = async () => await import ( '@/components/Replay/ReplayGrid' );

  export default {
    scrollToTop: true,

    head () {
      return {
        title: `Homepage - [bitwave.tv]`,
        link: [
          { rel: 'canonical', href: `https://bitwave.tv` },
        ],
        meta: [
          { name: 'og:title',       hid: 'og:title',       content: `Livestream Homepage - [bitwave.tv]` },
          { name: 'og:description', hid: 'og:description', content: `An open platform live streaming service for creators to freely express themselves.` },
          { name: 'og:image',       hid:'og:image',        content: this.poster},
          { name: 'description',    hid: 'description',    content: 'An open platform live streaming service for creators to freely express themselves.' },
          { name: 'twitter:card',        content: 'summary_large_image' },
          { name: 'twitter:site',        content: '@BitwaveTV' },
          { name: 'twitter:title',       content: 'Livestream Homepage - [bitwave.tv]' },
          { name: 'twitter:description', content: 'An open platform live streaming service for creators to freely express themselves.' },
          { name: 'twitter:image',       content: this.poster },
        ],
      }
    },

    components: {
      TrendingReplays,
      ReplayGrid,
      AboutUs,
      SimpleFooter,
      MessageOfTheDay,
      BannerVideo,
      StreamGrid,
    },

    data () {
      return {
        mounted: false,
        player: null,
        poster: 'https://cdn.bitwave.tv/static/img/Bitwave_Banner.jpg',
        chatMessages: null,
        offline: true,
        blurNSFW: true,
      }
    },

    methods: {

    },

    async asyncData ({ $axios }) {
      const defaultLive = [
        {
          "src": 'https://cdn.bitwave.tv/static/bumps/2a3un.mp4',
          "name": "offline",
          "type": "video/mp4",
        },
      ];

      // Timeout to prevent SSR from locking up
      const timeout = process.server ? process.env.SSR_TIMEOUT : 0;

      const getStreams = async () => {
        try {
          const { data } = await $axios.getSSR( 'https://api.bitwave.tv/v1/channels/live', { timeout } );
          if ( data && data.success ) {
            return {
              live: data.live,
              streamers: data.streamers,
            }
          } else {
            console.log( `API Error:`, data );
          }
        } catch ( error ) {
          console.error( `Failed to get live channels from API server: ${error.message}` );
          return {
            live: defaultLive,
            streamers: [],
          }
        }
        console.log( `Failed to get live channels from API server` );
        return {
          live: defaultLive,
          streamers: [],
        }
      };

      const streams = await getStreams();

      return {
        live: streams.live,
        streamers: streams.streamers,
        offline: false,
      }
    },

    computed: {
      mobile () {
        return this.mounted
          ? this.$vuetify.breakpoint.smAndDown
          : !this.$device.isDesktopOrTablet;
      },
    },

    mounted () {
      this.mounted = true;
      if ( this.offline ) this.$toast.error( 'API Error: SSR Hydration failed', { duration: 5000, icon: 'error', position: 'top-center' } );
    },
  }
</script>

