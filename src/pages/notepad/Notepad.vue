<template>

    <div>

        <Layout>
            <Header v-if="$route.path !== '/admin' && $route.path !== '/admin/login'" class="app_nav_header">
                <my-header v-bind:word="searchWord" v-on:changeWord="handle_search"></my-header>
            </Header>

            <Content class="body_page">
                <Row class="notepad_body">
                    <i-col span="6">
                        <div class="manage_part">
                            <div class="button_wrapper">
                                <router-link to="edit">
                                    <Button type="success" size="large" long icon="android-add">添加设备</Button>
                                </router-link>
                            </div>

<!--                            <div class="button_wrapper">-->
<!--                                <Button type="primary" size="large" long icon="android-settings">管理错题本</Button>-->
<!--                            </div>-->

                            <div class="card_wrapper">
                                <card>
                                    <div>
                                        <div class="card_title">
                                            系统信息
                                        </div>

                                        <div class="card_content">
                                            <Row>
                                                <i-col span="15">
                                                    <Row type="flex" justify="end">
                                                        设备总计：
                                                    </Row>
                                                </i-col>
                                                <i-col span="9">
                                                    <Row type="flex" justify="start">
                                                        {{q_data.length}}
                                                    </Row>
                                                </i-col>
                                            </Row>

                                            <Row>
                                                <i-col span="15">
                                                    <Row type="flex" justify="end">
                                                        打开的设备数：
                                                    </Row>
                                                </i-col>
                                                <i-col span="9">
                                                    <Row type="flex" justify="start">
                                                        {{opened_device}}
                                                    </Row>
                                                </i-col>
                                            </Row>

                                            <Row>
                                                <i-col span="15">
                                                    <Row type="flex" justify="end">
                                                        关闭的设备数：
                                                    </Row>
                                                </i-col>
                                                <i-col span="9">
                                                    <Row type="flex" justify="start">
                                                        {{q_data.length - opened_device}}
                                                    </Row>
                                                </i-col>
                                            </Row>

                                            <Row>
                                                <i-col span="15">
                                                    <Row type="flex" justify="end">
                                                        异常的设备数：
                                                    </Row>
                                                </i-col>
                                                <i-col span="9">
                                                    <Row type="flex" justify="start">
                                                        0
                                                    </Row>
                                                </i-col>
                                            </Row>


                                        </div>

                                    </div>
                                </card>
                            </div>

                            <div class="card_wrapper">
                                <card>
                                    <div>
                                        <div class="card_title">
                                            快速访问
                                        </div>

                                        <div class="card_content">
                                            <Tag color="blue">常用</Tag>
                                            <Tag>灯</Tag>
                                            <Tag>窗帘</Tag>
                                            <Tag>门</Tag>
                                            <Tag>客厅</Tag>
                                            <Tag>厨房</Tag>
                                            <Tag>卫生间</Tag>
                                            <Tag>卧室</Tag>
                                        </div>

                                    </div>
                                </card>
                            </div>
                            <div class="button_wrapper">
                                <Button type="default" size="large" long icon="ios-information-outline">关于本系统</Button>
                            </div>

                            <div class="button_wrapper">
                                <Button type="ghost" size="large" long icon="paper-airplane">我要反馈</Button>
                            </div>

                        </div>
                    </i-col>

                    <i-col span="17" offset="1">
                        <div class="right_part">

                            <div v-if="!(searchWord === null || searchWord === undefined) && searchWord.length !== 0"
                                 class="search-info">
                                <p>
                                    搜索结果： {{ this.searchWord }} &nbsp;&nbsp;
                                    <Button type="text" @click='cancel_search'>取消</Button>
                                </p>

                            </div>

                            <div class="settings">
                                <Row type="flex" align="middle">
                                    <i-col span="3">
                                        <Icon type="funnel"/>
                                        <span>筛选</span>
                                    </i-col>

                                    <i-col span="13">
                                        <RadioGroup v-model="radio_chosen" @on-change="switch_order">
                                            <Radio label="sort_by_time">按添加时间排序</Radio>
                                            <Radio label="sort_by_view">按使用次数排序</Radio>
                                            <!--<Radio label="sort_by_error">按错误率排序</Radio>-->
                                        </RadioGroup>
                                    </i-col>

                                    <i-col span="3">
                                        <Checkbox v-model="sort_order" @on-change="sort_data">降序</Checkbox>
                                    </i-col>

                                    <i-col span="5">
                                        <Row type="flex" justify="end" align="middle">
                                            <Checkbox v-model="only_not_review" @on-change="show_not_review_only">
                                                仅显示已连接的设备
                                            </Checkbox>
                                        </Row>


                                    </i-col>
                                </Row>
                            </div>

                            <div v-if="q_data.length !== 0">
                                <Row type="flex" justify="start" align="top">
                                    <i-col span="8">
                                        <div class="card-wrapper" v-for="(item, index) in q_data1">
                                            <div class="card-event-binder">
                                                <card class="card_content">


                                                    <Tag v-if="item.bySelf===false" color="blue">公共</Tag>
                                                    <Tag v-for="tag in item.qTag">
                                                        {{tag}}
                                                    </Tag>

                                                    <div class="item-title-wrapper">
                                                        <span class="item-title">{{item.title}}</span>
<!--                                                        <span class="item-title" @click="viewItem(item.id)">{{item.title}}</span>-->
                                                    </div>
                                                    <div class="item-switch-wrapper">
                                                        <i-switch v-model="q_data[index*3].switchStatus" @on-change="change_switch(index*3)">
                                                            <span slot="open">开</span>
                                                            <span slot="close">关</span>
                                                        </i-switch>
                                                    </div>
                                                    <Row>
                                                        <i-col span="12" class="col-manage edit">
                                                            <Button icon="edit" type="text" @click="edit(item.id)">编辑
                                                            </Button>
                                                        </i-col>
                                                        <i-col span="12" class="col-manage redo">
                                                            <Button icon="android-delete" type="text" @click="delete_topic=true;deleted_item_id=item.id;">删除</Button>
                                                        </i-col>

                                                        <!--<i-col span="6" class="col-manage del">-->
                                                        <!--<Button type="text">删除</Button>-->
                                                        <!--</i-col>-->
                                                    </Row>



                                                </card>
                                            </div>

                                        </div>
                                    </i-col>
                                    <i-col span="8">
                                        <div class="card-wrapper" v-for="(item, index) in q_data2">
                                            <div class="card-event-binder">
                                                <card class="card_content">


                                                    <Tag v-if="item.bySelf===false" color="blue">公共</Tag>
                                                    <Tag v-for="tag in item.qTag">
                                                        {{tag}}
                                                    </Tag>

                                                    <div class="item-title-wrapper">
                                                        <span class="item-title">{{item.title}}</span>
                                                        <!--                                                        <span class="item-title" @click="viewItem(item.id)">{{item.title}}</span>-->
                                                    </div>
                                                    <div class="item-switch-wrapper">
                                                        <i-switch v-model="q_data[index*3+1].switchStatus" @on-change="change_switch(index*3+1)">
                                                            <span slot="open">开</span>
                                                            <span slot="close">关</span>
                                                        </i-switch>
                                                    </div>
                                                    <Row>
                                                        <i-col span="12" class="col-manage edit">
                                                            <Button icon="edit" type="text" @click="edit(item.id)">编辑
                                                            </Button>
                                                        </i-col>
                                                        <i-col span="12" class="col-manage redo">
                                                            <Button icon="android-delete" type="text" @click="redo(item.id)">删除</Button>
                                                        </i-col>

                                                        <!--<i-col span="6" class="col-manage del">-->
                                                        <!--<Button type="text">删除</Button>-->
                                                        <!--</i-col>-->
                                                    </Row>



                                                </card>
                                            </div>

                                        </div>
                                    </i-col>
                                    <i-col span="8">
                                        <div class="card-wrapper" v-for="(item, index) in q_data3">
                                            <div class="card-event-binder">
                                                <card class="card_content">


                                                    <Tag v-if="item.bySelf===false" color="blue">公共</Tag>
                                                    <Tag v-for="tag in item.qTag">
                                                        {{tag}}
                                                    </Tag>

                                                    <div class="item-title-wrapper">
                                                        <span class="item-title">{{item.title}}</span>
                                                        <!--                                                        <span class="item-title" @click="viewItem(item.id)">{{item.title}}</span>-->
                                                    </div>
                                                    <div class="item-switch-wrapper">
                                                        <i-switch v-model="q_data[index*3+2].switchStatus" @on-change="change_switch(index*3+2)">
                                                            <span slot="open">开</span>
                                                            <span slot="close">关</span>
                                                        </i-switch>
                                                    </div>
                                                    <Row>
                                                        <i-col span="12" class="col-manage edit">
                                                            <Button icon="edit" type="text" @click="edit(item.id)">编辑
                                                            </Button>
                                                        </i-col>
                                                        <i-col span="12" class="col-manage redo">
                                                            <Button icon="android-delete" type="text" @click="redo(item.id)">删除</Button>
                                                        </i-col>

                                                        <!--<i-col span="6" class="col-manage del">-->
                                                        <!--<Button type="text">删除</Button>-->
                                                        <!--</i-col>-->
                                                    </Row>



                                                </card>
                                            </div>

                                        </div>
                                    </i-col>

                                </Row>

                                <Modal v-model="delete_topic" width="360">
                                    <p slot="header" style="color:#f60;text-align:center">
                                        <Icon type="information-circled"></Icon>
                                        <span>删除确认</span>
                                    </p>
                                    <div style="text-align:center">
                                        <p>你确定要永久删除这个设备吗？</p>
                                    </div>
                                    <div slot="footer">
                                        <Row type="flex" justify="space-around">
                                            <i-col span="11">
                                                <Button type="warning" size="large" long :loading="modal_loading"
                                                        @click="del">删除
                                                </Button>
                                            </i-col>
                                            <i-col span="11">
                                                <Button type="default" size="large" long :disabled="modal_loading"
                                                        @click="cancel_del">取消
                                                </Button>
                                            </i-col>
                                        </Row>
                                    </div>
                                </Modal>

                                <div class="card-wrapper">
                                    <Button type="ghost" long size="large" icon="load-a" @click="load_more">加载更多</Button>
                                </div>
                            </div>

                            <div v-else>
                                <card>没有设备</card>
                            </div>

                        </div>
                    </i-col>

                </Row>
            </Content>

            <Footer>
                <my-footer></my-footer>
            </Footer>
        </Layout>


    </div>

</template>

<script>
    import myHeader from '../../components/Header'
    import myFooter from '../../components/Footer'
    import Card from "iview/src/components/card/card";
    import Icon from "iview/src/components/icon/icon";
    import ICol from "iview/src/components/grid/col";
    import ISwitch from "iview/src/components/switch/switch";

    // import Info from "../../assets/data-notepad.json"
    import * as util from '../../utils/util'

    export default {
        components: {
            myHeader,
            myFooter,
            ISwitch,
            ICol,
            Icon,
            Card
        },
        name: "main",

        data: () => ({
            q_index: -1,
            all_data: [],
            q_data: [],
            page: 0,
            switches: [],
            delete_topic: false,
            modal_loading: false,
            deleted_item_id: "",

            searchWord: "",

            radio_chosen: "sort_by_time",
            sort_order: true,
            only_not_review: false,
            // show_search_tip: false

        }),

        mounted: function () {
            // if (this.$route.query.word) {
            //     this.search_word = this.$route.query.word;
            //     // this.$Message.success(this.search_word);
            //     this.show_search_tip = true;
            //     this.q_data = this.q_data.filter((item_data) => {
            //         return item_data.qTag.indexOf(this.search_word) >= 0
            //     })
            // }
            //
            // alert("> mounted");

            this.requestData(this.page, "time");

            // this.request_and_sort_data(this.radio_chosen);
        },

        methods: {
            requestData: function (page, method, word = "") {
                let xhr = new XMLHttpRequest();
                xhr.open("GET", "http://192.168.0.106:8080/item/list?page=" + page + "&method=" + method + "&word=" + word, true);
                xhr.withCredentials = true;
                xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded");

                xhr.onreadystatechange = () => {
                    console.log(xhr.readyState + "  " + xhr.status);
                    if (xhr.readyState === 4 && xhr.status === 200) {
                        console.log(xhr.responseText);
                        let listResponse = JSON.parse(xhr.responseText);
                        console.log(listResponse);
                        console.log(listResponse.status);
                        if (listResponse.status) {
                            if (listResponse.num === 0) {
                                this.$Message.info("没有更多了");
                                return;
                            }
                            let new_items = listResponse.items;
                            this.all_data.push(...new_items);
                            [...(this.q_data)] = this.all_data;
                            this.page += 1;
                        }
                    }
                };
                xhr.send();
            },

            load_more: function () {
                console.log("> load_more");
                this.requestData(this.page, this.radio_chosen === "sort_by_time" ? "time" : "view", this.searchWord);
            },

            showManage: function (index) {
                this.q_index = index;
            },

            hideManage: function () {
                this.q_index = -1;
            },

            viewItem: function (index) {
                this.$router.push({name: 'Item', query: {id: index}});
            },

            redo: function (item_id) {
                this.$router.push({name: 'Item', query: {id: item_id, redo: true}});
            },

            del: function () {
                this.modal_loading = true;
                setTimeout(() => {
                    this.delete_topic = false;
                    this.modal_loading = false;

                    let xhr = new XMLHttpRequest();
                    xhr.open("DELETE", "http://192.168.0.106:8080/item/entry?id=" + this.deleted_item_id, true);
                    xhr.withCredentials = true;
                    xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded");

                    xhr.onreadystatechange = () => {
                        console.log(xhr.readyState + "  " + xhr.status);
                        if (xhr.readyState === 4 && xhr.status === 200) {
                            console.log(xhr.responseText);
                            let deleteResponse = JSON.parse(xhr.responseText);
                            console.log(deleteResponse);
                            console.log(deleteResponse.status);
                            if (deleteResponse.status) {
                                this.$Message.success('删除成功！');
                                // this.$router.push("notepad");
                                this.$router.go(0);
                            } else {
                                this.$Message.error('删除失败，请重试');

                            }
                        }
                    };
                    xhr.send();
                }, 2000);
            },

            cancel_del: function () {
                this.delete_topic = false;
            },

            edit: function (item_id) {
                this.$router.push({name: 'Edit', query: {id: item_id}});
            },

            sort_data: function () {
                if (this.radio_chosen === 'sort_by_time') {
                    // this.requestData(this.page, "time");

                    this.q_data.sort((obj1, obj2) => {
                        // this.$Message.success(obj1.createTime.toString());
                        let diff = obj1.createTime < obj2.createTime;
                        return (this.sort_order) ? diff : !diff;
                    });
                } else if (this.radio_chosen === 'sort_by_view') {
                    // this.requestData(this.page, "view");

                    this.q_data.sort((obj1, obj2) => {
                        let diff = obj1.viewCount < obj2.viewCount;
                        return (this.sort_order) ? diff : !diff;
                    });
                }


                // this.request_and_sort_data(this.radio_chosen);
            },

            switch_order: function (cur) {
                this.all_data = [];
                this.q_data = [];
                this.page = 0;
                this.requestData(this.page, (cur === "sort_by_time") ? "time" : "view", this.searchWord);
                // this.sort_data();
                // } else if (cur === 'sort_by_error') {
                //     // console.log(this.q_data);
                //     this.q_data.sort((obj1, obj2) => {
                //         let obj1_rate = obj1.redoLite.redoCorrectCount / obj1.redoLite.redoCount;
                //         let obj2_rate = obj2.redoLite.redoCorrectCount / obj2.redoLite.redoCount;
                //         let diff = obj1_rate < obj2_rate;
                //         return (this.sort_order) ? diff : !diff;
                //     })
                // }
            },

            show_not_review_only: function () {
                if (this.only_not_review) {
                    this.q_data = this.q_data.filter((item) => {
                        return item.redoCount === 0
                    });
                } else {
                    [...(this.q_data)] = this.all_data;
                    this.sort_data();
                }
            },

            parse_time: function (sec) {
                if (typeof sec === 'number') {
                    let date = new Date(sec);
                    return date.getFullYear()
                        + "/" + this.PrefixInteger(date.getMonth() + 1)
                        + "/" + this.PrefixInteger(date.getDate())
                        + " " + this.PrefixInteger(date.getHours())
                        + ":" + this.PrefixInteger(date.getMinutes())
                        + ":" + this.PrefixInteger(date.getSeconds());
                } else {
                    return sec;
                }
            },

            /**
             * @return {string}
             */
            PrefixInteger: function (num) {
                let num_str = num.toString();
                if (num_str.length === 1) {
                    num_str = "0" + num_str;
                }
                return num_str;
            },

            handle_search: function (word) {
                console.log("received word: " + word);
                this.searchWord = word;

                this.all_data = [];
                this.q_data = [];
                this.page = 0;
                this.requestData(this.page, "time", word);

            },

            cancel_search: function () {
                this.page = 0;
                this.searchWord = null;
                console.log(">>> " + this.page);
                this.requestData(this.page, "time");
            },

            change_switch: function (idx) {

                let edit_form = {
                    id: this.q_data[idx].id,
                    switchStatus: this.q_data[idx].switchStatus,
                };
                console.log(edit_form);
                let data = JSON.stringify(edit_form);
                console.log(data);

                data = encodeURI(encodeURIComponent(data));

                console.log(data);

                let xhr = new XMLHttpRequest();
                xhr.open("POST", "http://192.168.0.106:8080/item/entry", true);
                xhr.withCredentials = true;
                xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded");

                xhr.onreadystatechange = () => {
                    console.log(xhr.readyState + "  " + xhr.status);
                    if (xhr.readyState === 4 && xhr.status === 200) {
                        console.log(xhr.responseText);
                        let registerResponse = JSON.parse(xhr.responseText);
                        console.log(registerResponse);
                        console.log(registerResponse.status);
                    }
                };
                xhr.send("json=" + data);

                if (this.q_data[idx].switchStatus)
                    this.$Message.success('打开设备成功！');
                else
                    this.$Message.success('关闭设备成功！');
            }
        },

        // watch: {
        //     // '$route'(to, from) {
        //     //     if (to.query.word) {
        //     //         if (!from.query.word || (from.query.word && to.query.word !== from.query.word)) {
        //     //             this.$router.go(0);
        //     //             // this.$Message.success(to.query.word + " / " + from.query.word);
        //     //         }
        //     //     } else {
        //     //         this.$route.go(0);
        //     //     }
        //     // },
        //
        //     searchWord(curVal, oldVal) {
        //         if (curVal) {
        //             console.log("> value_changed_to: " + this.searchWord);
        //             this.$Message.success(this.searchWord);
        //
        //         }
        //     },
        // },

        computed: {
            q_data1: function () {
                return this.q_data.filter((_, index) => { return index % 3 === 0; })
            },
            q_data2: function () {
                return this.q_data.filter((_, index) => { return index % 3 === 1; })
            },
            q_data3: function () {
                return this.q_data.filter((_, index) => { return index % 3 === 2; })
            },
            opened_device: function () {
                return this.q_data.filter((item) => { return item.switchStatus === true; }).length;
            }
        }
    }
</script>

<style scoped>

    .notepad_body {
        /*padding-top: 0px;*/
    }

    .manage_part {
        padding-top: 10px;
    }

    .manage_part .button_wrapper, .card_wrapper {
        padding-bottom: 15px;
    }

    .right_part .card-wrapper {
        padding: 10px;
    }

    .right_part .card_content {
        text-align: center;
    }

    .settings {
        padding: 10px;
        text-align: left;
    }

    .manage_part .card_title {
        padding-bottom: 8px;
        border-bottom: 1px dashed #dddee1;

        color: #80848f;
    }

    .manage_part .card_content {
        padding-top: 10px;

    }

    .card_topic_html {
        margin-top: 10px;

        height: 60px;
        overflow: hidden;
    }

    .item-time {
        color: #80848f;
        font-size: 12px;
    }

    .item-title {
        font-size: 18px;

        padding: 10px 5px;

        cursor: pointer;
    }

    .item-stat {
        padding: 0 5px;

        font-size: 12px;
        color: #80848f;
    }

    .item-stat .number {
        color: #495060;
        font-size: 14px;
    }

    .search-info {
        padding: 10px 5px;

        text-align: left;

        font-size: 16px;
    }

    .item-title-wrapper {
        padding: 30px 0;
    }
    .item-switch-wrapper {
        padding: 0 20px 20px;
    }
</style>
