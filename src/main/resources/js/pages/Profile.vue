<template xmlns="http://www.w3.org/1999/html">
    <div class="background-profile">
        <v-container>
            <v-row align="start"
                   justify="center"
                   style="margin-bottom: 34px"
            >
                <v-col :sm="isMobile?'6':'5'"
                       lg="4"
                       class="pt-0"
                >
                    <user-name-and-city v-if="isMobile"
                                        :myProfile="myProfile"
                                        :isMobile="isMobile"
                    ></user-name-and-city>
                    <image-main :profileUserGuest="profileUserGuest" :myProfile="myProfile" :isMobile="isMobile"
                                :class="isMobile? 'pt-0':'pt-8'"></image-main>
                </v-col>
                <v-col sm="7"
                       lg="8"
                >
                    <v-row align="center"
                           justify="center"
                           :class="!isMobile ?'ml-2':''">
                        <v-col class="pa-0">
                            <user-name-and-city v-if="!isMobile"
                                                :myProfile="myProfile"
                                                :isMobile="isMobile"
                            ></user-name-and-city>
                            <image-slide :myProfile="myProfile" :isMobile="isMobile"></image-slide>
                            <v-divider class="ma-5"></v-divider>
                            <about
                                    :myProfile="myProfile"
                                    :profileDetails="profileDetails"
                                    :profileUserGuest="profileUserGuest"
                            ></about>
                        </v-col>
                    </v-row>
                </v-col>
            </v-row>
        </v-container>
    </div>
</template>

<script>
    import ImageSlide from "../components/profile/ImageSlide.vue";

    import UserNameAndCity from "../components/profile/UserNameAndCity.vue";
    import About from "../components/profile/About.vue";
    import ImageMain from "../components/profile/ImageMain.vue";
    import {mapActions, mapMutations, mapState} from "vuex";

    export default {
        components: {
            ImageMain,
            About,
            ImageSlide,
            UserNameAndCity,
        },
        name: "Profile",
        data: () => ({
            myProfile: null,
        }),
        created() {
            this.updateProfile()
        },
        watch: {
            '$route'() {
                this.updateProfile()
            }
        },
        mounted() {
            if (this.isMobile)
                this.mobileNavigationMutation(true)
        },
        computed: {
            ...mapState({
                profileUserGuest: state => state.storeUserGuest.userGuest,
                profileDetails: state => state.storeProfileDetails.profileDetails,
                isMobile: state => state.storeUserProfile.isMobile,
            }),
        },
        methods: {
            ...mapMutations(['mobileNavigationMutation']),
            ...mapActions(['setProfileUserGuestAction']),
            async updateProfile() {
                if (!window.location.pathname.toString().includes('my')) {
                    const id = Number(this.$route.params.id)
                    if (this.profileUserGuest === null) {
                        this.setProfileUserGuestAction(id)
                    } else {
                        if (this.profileUserGuest.id !== id) this.setProfileUserGuestAction(id)
                    }
                    this.myProfile = false
                } else {
                    this.myProfile = true
                }
            }
        }
    }

</script>

<style scoped>
    .background-profile {
        background-color: #f2f3f5;
    }

</style>