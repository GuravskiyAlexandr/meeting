<template>
    <div>
        <v-container>
            <v-expansion-panels v-model="panel" multiple>
                <v-expansion-panel>
                    <v-expansion-panel-header><h2 class="title mr-4">Поиск</h2></v-expansion-panel-header>
                    <v-expansion-panel-content>
                        <v-row>
                            <v-col>
                                <v-row style="background-color: white" class="pa-5" justify="center" align="center">
                                    <form autocomplete="off">
                                        <autocomplete-city :locale-search="localeSearch()" class="mr-4"
                                                           style="width: 300px"
                                                           ref="formCity">
                                        </autocomplete-city>
                                    </form>
                                    <h2 class="title">Возрост</h2>
                                    <v-col class="px-2 pt-10">
                                        <v-range-slider
                                                v-model="range"
                                                :max="max"
                                                :min="min"
                                                hide-details
                                                class="align-center"
                                                thumb-label="always"
                                        >
                                        </v-range-slider>
                                    </v-col>
                                </v-row>
                                <v-col>
                                    <v-row justify="end">
                                        <v-btn dark color="#1976d2" large @click="getUsersSearch()"> Найти</v-btn>
                                    </v-row>
                                </v-col>
                            </v-col>
                        </v-row>
                    </v-expansion-panel-content>
                </v-expansion-panel>
            </v-expansion-panels>
        </v-container>
        <div style="background-color: #f5f6fa">
            <v-container class="pt-12">
                <v-row>
                    <card-view v-for="(user, i) in getSearchUsers"
                               :key="user.id"
                               class="ma-auto"
                               :user="user"
                               :userProfile="userProfile"
                    ></card-view>
                </v-row>
                <lazy-loader :functionLoader="getUsersSearch"
                             :list="stateUsers"
                ></lazy-loader>
            </v-container>
        </div>
    </div>
</template>

<script>
    import {mapMutations, mapActions, mapState} from 'vuex'
    import LikedButton from "../components/profile/LikedButton.vue";
    import CardView from "../components/searchCard/CardView.vue";
    import LazyLoader from "../components/pageViews/LazyLoader.vue";
    import getAge from "../util/helper/getAge";
    import AutocompleteCity from "../components/profile/AutocompleteCity.vue";

    const getFunctionLodash = () => import("../util/helper/functionLodash");

    export default {
        name: "Search",
        components: {AutocompleteCity, LazyLoader, CardView, LikedButton},
        data: () => ({
            img: null,
            min: 16,
            max: 70,
            range: [17, 70],
            model: '',
            panel: [0],
        }),

        mounted() {
            if (this.mobileNavigationMutation)
            this.mobileNavigationMutation(true)
            if (this.stateUsers.users === undefined) setTimeout(() => this.getUsersSearch, 1000)
        },
        computed: {
            ...mapMutations(['mobileNavigationMutation']),
            ...mapState({
                userProfile: state => state.storeUserProfile.userProfile,
                stateUsers: state => state.storeUserGuest.users
            }),
            getSearchUsers() {
                if (this.stateUsers.listFromDto !== undefined) {
                    const users = this.stateUsers.listFromDto
                    users.sort((a, b) => (getAge(a.birthDate) - getAge(b.birthDate)))
                    return users
                }
            }
        },
        methods: {
            ...mapActions(['getListUsersAction', 'getListUsersButtonAction']),
            ...mapMutations(['writeNewMessageAndNewChatMutation']),

            localeSearch() {
                return localStorage.getItem('locale' + this.userProfile.id)
            },
            getUsersSearch(page) {
                let localeType = null
                let localeName = null
                if (this.$refs.formCity.search !== null && this.$refs.formCity.search !== undefined) {
                    if (typeof this.$refs.formCity.locale === 'string') {
                        localeName = this.$refs.formCity.locale
                    } else {
                        localeName = this.$refs.formCity.locale.name
                    }
                    getFunctionLodash().then(value => {
                        localeType = value.getLocale(localeName, this.$refs.formCity.entries)
                        this.setParamsUserSearch(localeType, localeName, page)
                    })
                } else {
                    this.setParamsUserSearch(localeType, localeName, page)
                }
            },
            setParamsUserSearch(localeType, localeName, page) {
                localStorage.setItem('locale' + this.userProfile.id, localeName)
                if (localeName !== undefined && localeType !== undefined) {
                    if (page === undefined) page = 0
                    const user = {
                        ageMin: this.range[0],
                        ageMax: this.range[1],
                        gender: this.userProfile.gender,
                        localeName: localeName,
                        localeType: localeType,
                    }
                    const userPage = {
                        user,
                        page
                    }
                    this.getListUsersAction(userPage)
                    this.panel = []
                }
            }
        }
    }
</script>

<style scoped>
</style>