<template>
    <v-app>
        <!--メインメニュー（左）-->
        <v-navigation-drawer v-model="drawer"
                             app>
            <v-list dense>
                <v-list-item-group v-model="pageNum"
                                   :color="$baseColor1">
                    <v-list-item @click="selectMainMenu(PAGE_BRANCHES)">
                        <v-list-item-content>
                            <v-badge v-if="alertCount.dealers > 0"
                                     :content="alertCount.dealers"
                                     inline
                                     :color="$baseColor1">
                                <v-list-item-title>
                                    支店
                                </v-list-item-title>
                            </v-badge>
                            <v-list-item-title v-else>
                                支店
                            </v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item @click="selectMainMenu(PAGE_MANAGERS)">
                        <v-list-item-content>
                            <v-badge v-if="alertCount.managers > 0"
                                     :content="alertCount.managers"
                                     inline
                                     :color="$baseColor1">
                                <v-list-item-title>
                                    マネージャー
                                </v-list-item-title>
                            </v-badge>
                            <v-list-item-title v-else>
                                マネージャー
                            </v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item @click="selectMainMenu(PAGE_STAFFS)">
                        <v-list-item-content>
                            <v-badge v-if="alertCount.staffs > 0"
                                     :content="alertCount.staffs"
                                     inline
                                     :color="$baseColor1">
                                <v-list-item-title>
                                    スタッフ
                                </v-list-item-title>
                            </v-badge>
                            <v-list-item-title v-else>
                                スタッフ
                            </v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item @click="selectMainMenu(PAGE_ITEMS)">
                        <v-list-item-content>
                            <v-badge v-if="alertCount.items > 0"
                                     :content="alertCount.items"
                                     inline
                                     :color="$baseColor1">
                                <v-list-item-title>
                                    商品
                                </v-list-item-title>
                            </v-badge>
                            <v-list-item-title v-else>
                                商品
                            </v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item @click="selectMainMenu(PAGE_REPORT)">
                        <v-list-item-content>
                            <v-badge v-if="alertCount.report > 0"
                                     :content="alertCount.report"
                                     inline
                                     :color="$baseColor1">
                                <v-list-item-title>
                                    レポート
                                </v-list-item-title>
                            </v-badge>
                            <v-list-item-title v-else>
                                レポート
                            </v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                </v-list-item-group>
            </v-list>
        </v-navigation-drawer>

        <!--アプリケーションヘッダ-->
        <v-app-bar app
                   :style="$baseColorStyle"
                   dark>
            <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
            <v-toolbar-title>{{pageName}}</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-menu offset-y>
                <template v-slot:activator="{ on, attrs }">
                    <v-btn icon
                           v-bind="attrs"
                           v-on="on">
                        <v-icon>mdi-dots-vertical</v-icon>
                    </v-btn>
                </template>
                <v-list>
                    <v-list-item @click="$refs.profileModal.open()">
                        <v-list-item-title>プロフィール</v-list-item-title>
                    </v-list-item>
                    <v-list-item @click="logout">
                        <v-list-item-title>ログアウト</v-list-item-title>
                    </v-list-item>
                </v-list>
            </v-menu>
        </v-app-bar>

        <!--メインコンテンツ-->
        <v-main>
            <Branches v-if="page === PAGE_BRANCHES"
                      @reload="reload"></Branches>
            <Managers v-else-if="page === PAGE_MANAGERS"
                      @reload="reload"></Managers>
            <Staffs v-else-if="page === PAGE_STAFFS"
                    @reload="reload"></Staffs>
            <Items v-else-if="page === PAGE_ITEMS"
                   @reload="reload"></Items>
            <Report v-else-if="page === PAGE_REPORT"></Report>
        </v-main>
        <ProfileModal ref="profileModal"></ProfileModal>
    </v-app>
</template>

<script>
    import LogoImg from "@/assets/images/logo.png";

    import ProfileModal from "@/pages/mypage/components/profileModal.vue";
    import Branches from "@/pages/mypage/branches/branches.vue";
    import Managers from "@/pages/mypage/managers/managers.vue";
    import Staffs from "@/pages/mypage/staffs/staffs.vue";
    import Items from "@/pages/mypage/items/items.vue";
    import Report from "@/pages/mypage/report/report.vue";

    const PAGE_BRANCHES = "branches";
    const PAGE_MANAGERS = "managers";
    const PAGE_STAFFS = "staffs";
    const PAGE_ITEMS = "items";
    const PAGE_REPORT = "report";

    export default {
        name: "mypage",
        components: {
            Report,
            Items,
            Staffs,
            Managers,
            Branches,
            ProfileModal,
        },
        data () {
            return {
                PAGE_BRANCHES: PAGE_BRANCHES,
                PAGE_MANAGERS: PAGE_MANAGERS,
                PAGE_STAFFS: PAGE_STAFFS,
                PAGE_ITEMS: PAGE_ITEMS,
                PAGE_REPORT: PAGE_REPORT,

                logoImg: LogoImg,

                drawer: null,
                page: null,
                pageNum: 0,
                pageName: null,
                alertCount: {
                    dealers: 0
                }
            }
        },
        created () {
            // 通知数を取得
            this.reload();

            this.selectMainMenu(this.PAGE_BRANCHES);
        },
        methods: {
            /**
             * メインメニュー選択時の処理
             * @param to
             */
            selectMainMenu(to)
            {
                this.page = to;
                // 現在のページを特定
                if (to === PAGE_BRANCHES) {
                    this.pageName = "支店";
                    this.pageNum = 0;
                } else if (to === PAGE_MANAGERS) {
                    this.pageName = "マネージャー";
                    this.pageNum = 1;
                } else if (to === PAGE_STAFFS) {
                    this.pageName = "スタッフ";
                    this.pageNum = 2;
                } else if (to === PAGE_ITEMS) {
                    this.pageName = "商品";
                    this.pageNum = 3;
                } else if (to === PAGE_REPORT) {
                    this.pageName = "レポート";
                    this.pageNum = 4;
                }
            },

            /**
             *
             */
            getItemsAlertCount()
            {
                this.$axios.get("item_allocation_requests?search_status=waiting")
                    .then(res => {
                        this.alertCount.items = res.data.total_count;
                        this.$forceUpdate();
                    });
            },

            /**
             * 各種通知情報をリロードする
             */
            reload()
            {
                this.getItemsAlertCount();
            },
            /**
             * ログアウトする
             */
            logout()
            {
                this.$axios.post("/logout")
                    .then(res => {
                      this.$cookies.remove("manager.access_token");
                      this.$cookies.remove("manager.admin_id");
                      this.$router.push('/login');
                    })
                    .catch(e => {
                      this.$cookies.remove("manager.access_token");
                      this.$cookies.remove("manager.admin_id");
                      this.$router.push('/login');
                    });
            }
        }
    }
</script>

<style scoped>
    .header {
        box-shadow: 0px 0px 5px;
        background-color: #FFF;
    }
    .badge {
        background-color: red;
        color: #FFFFFF;
        line-height: normal;
        border-radius: 20px;

        padding: 5px 8px 5px 8px;
        height: 25px;
        position: absolute;
        top: 14px;
        right: 15px;
    }
</style>
