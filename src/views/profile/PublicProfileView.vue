<template>
  <div class="container center">
    <template v-if="loading">
      <spinner :show="loading"></spinner>
    </template>
    <template v-else-if="error">
      <div class="bg-danger"> Error: {{ error }}</div>
    </template>
    <template v-else>
      <div class="profile-view col-md-12">
        <profile-details
        :userData="user" />
      </div>
    </template>
  </div>
</template>

<script>
import { mapActions, mapState } from 'vuex'
import ProfileDetails from '@/components/profile/ProfileDetails'
import Spinner from '@/components/Spinner'

export default {
  name: 'public-profile-view',
  components: {
    ProfileDetails,
    Spinner
  },
  data () {
    return {
      loading: false,
      error: null,
      user: null
    }
  },
  created () {
    this.fetchData()
  },
  watch: {
    // re-fetch if route changes
    '$route': 'fetchData'
  },

  methods: {
    ...mapActions(['fetchPublicProfileData']),
    async fetchData () {
      this.loading = true
      try {
        const response = await this.fetchPublicProfileData({ userId: this.userId })
        this.user = response.data
      }
      catch (error) {
        this.error = error.response.data.message
      }
      finally {
        this.loading = false
      }
    }
  },

  computed: {
    ...mapState({
      userId (state) {
        return state.route.params.id
      }
    })
  }
}
</script>
<style lang="stylus">
  .center
    text-align center
    margin 5vh 0
</style>
