<template>
    <div>

      <v-sheet
        class="py-2 px-3 hide-scrollbar bw-channel-topbar"
        color="accentwave"
        tile
      >
        <div class="d-flex align-center justify-space-between">
          <div class="d-flex align-center grey--text">
            <v-avatar size="32">
              <img
                v-if="avatar"
                :src="`${avatar}`"
                :alt="name"
              />
              <v-icon v-else>warning</v-icon>
            </v-avatar>
            <div class="mx-2">{{ name }}</div>
          </div>

          <div class="d-flex align-center">

            <!-- NSFW Icon -->
            <template v-if="nsfw">
              <div class="font-weight-bold green--text body-2">NSFQ</div>
              <v-divider vertical class="mx-2"/>
            </template>

            <!-- Admin Button -->
            <KickStreamButton
              v-if="isAdmin"
              :streamer="name"
            />

            <!-- Follow Button -->
            <FollowButton
              :streamer-id="streamerId"
            />

          </div>
        </div>
      </v-sheet>

    </div>
</template>

<script>
  import { mapGetters } from 'vuex';
  import { VStore } from '@/store';
  import FollowButton from '@/components/Channel/FollowButton';
  const KickStreamButton = async () => await import( '@/components/Admin/KickStreamButton' );

  export default {
    name: 'StreamTopBar',

    components: {
      FollowButton,
      KickStreamButton,
    },

    props: {
      avatar:     { type: String },
      name:       { type: String },
      nsfw:       { type: Boolean },
      streamerId: { type: String },
    },

    data() {
      return {};
    },

    computed: {
      ...mapGetters({
        isAdmin  : VStore.$getters.isAdmin,
      }),
    },
  };
</script>
