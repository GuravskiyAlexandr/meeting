<template>
    <v-bottom-navigation
            fixed
            color="deep-purple accent-4"
            grow
    >
        <v-btn min-width="60" to="/">
            <span>Поиск</span>
            <v-icon> mdi-account-search-outline</v-icon>
        </v-btn>

        <v-btn min-width="60"
               to="/sympathy"
        >
            <span>Симпатии</span>
            <v-badge v-if="getNewLike()"
                     icon="notifications"
                     overlap
                     :value="badgeValue"
                     color="pink"
            >
                <v-icon>mdi-heart</v-icon>
            </v-badge>
        </v-btn>

        <v-btn min-width="60"
               to="/message"
        >
            <span>почта</span>
            <v-badge color="pink"
                     overlap
                     :content="messages"
                     :value="messages"
                     v-if="getCountNewMessages()"
            >
                <v-icon>mail</v-icon>
            </v-badge>
        </v-btn>
        <v-btn min-width="60"
               to="/views"
        >
            <span>Просмотры</span>
            <v-icon>visibility</v-icon>
        </v-btn>

        <v-menu top offset-y>
            <template v-slot:activator="{ on }">
                <v-btn min-width="60" v-on="on">
                    <span>Ещё</span>
                    <v-icon>mdi-menu</v-icon>
                </v-btn>
            </template>
            <v-list>
                <v-list-item
                        v-for="(item, index) in itemsMenu"
                        :key="index"
                        :to="item.to"
                        @click="selectItemMenu(item)"
                >
                    <v-list-item-title>
                        {{item.title}}
                    </v-list-item-title>
                </v-list-item>
            </v-list>
        </v-menu>
    </v-bottom-navigation>

</template>

<script>
    import {mapMutations} from "vuex";

    export default {
        props: {
            chats: Array,
            userProfile:Object
        },
        name: "MobileNavigation",
        data: () => ({
            badgeValue: true,
            messages: 0,
            itemsMenu: [
                {title: 'Профиль', to: '/profile/my'},
                {title: 'Настройки', to: '/settings'},
                {title: 'Выход', to: '/logout'},
            ],
        }),

        methods: {
            ...mapMutations(['logoutUserMutation']),
            getNewLike() {
                this.userProfile.likeNew === 1 ? this.badgeValue = true : this.badgeValue = false
                return true
            },
            selectItemMenu(item) {
                if (item.to === '/logout') {
                    this.$http.get('/logout')
                    this.logoutUserMutation(null)
                }
            },
            getCountNewMessages() {
                let count = 0
                this.chats.forEach(item => {
                    count += item.numberOfNewMessage
                })
                if (count === 0 || isNaN(count)) {
                    this.messages = 0
                } else {
                    this.messages = count
                }
                return true
            }
        }
    }
</script>

<style scoped>
    .v-btn span {
        font-size: 9px;
    }
</style>