<template>
    <el-card class="box chat-box">
        <el-row v-if="my">
            <div class="content">
                <div class="text-right item" v-for="item in message_list" :key="item">
                    <img src="https://blog-ico.oss-cn-shanghai.aliyuncs.com/1.jpg" class="'float-right" />
                    <div class="con">
                        <span>{{item.source_name}}</span>
                        <p>{{item.message}}</p>
                    </div>
                </div>
            </div>
            <el-input
                class="message"
                type="textarea"
                :rows="4"
                resize="none"
                placeholder="(ctrl + enter发送消息)"
                v-model="message"
                @keyup.enter.native.ctrl="push">
            </el-input>
        </el-row>
    </el-card>
</template>

<script>
import {Row, Col, Input} from 'element-ui';
import Login from '@/components/pages/chat/Login';

export default {
    components: {
        [Row.name]: Row,
        [Col.name]: Col,
        [Input.name]: Input,
        Login,
    },
    data() {
        return {
            loading: false,
            websock: null,
            message: null,
            message_list: [],
            ws_data: {
                data: null,
            },
		};
    },
    methods: {
        push() {
			this.ws_data.data = this.message;
			this.message = null;
            //若是ws开启状态
            if (this.websock.readyState === this.websock.OPEN) {
                this.websocketsend()
            }
            // 若是 正在开启状态，则等待300毫秒
            else if (this.websock.readyState === this.websock.CONNECTING) {
                let that = this;//保存当前对象this
                setTimeout(function () {
                    that.websocketsend()
                }, 300);
            }
            // 若未开启 ，则等待500毫秒
            else {
                this.initWebSocket();
                let that = this;//保存当前对象this
                setTimeout(function () {
                    that.websocketsend()
                }, 500);
            }
        },
        initWebSocket() { //初始化weosocket
            //ws地址
            const wsuri = 'wss://wrath.cc/ws/chat/';
            // const wsuri = 'ws://localhost:9501';
            this.websock = new WebSocket(wsuri);
            this.websock.onmessage = this.websocketonmessage;
            this.websock.onclose = this.websocketclose;
        },
        websocketonmessage(e){ //数据接收
            let res = JSON.parse(e.data);
            console.log(res);
        },
        websocketsend() {//数据发送
            this.websock.send(JSON.stringify(this.ws_data));
        },
    },
    mounted() {
        this.initWebSocket();
    },
    beforeDestroy() {
        this.websock.onclose;
    },
};
</script>
<style>
.chat-box .el-card__body {
    padding-left: 0px !important;
}
</style>

<style lang="scss">
.box {
    width: 50%;
    margin: auto;
    .user-list {
        height: 500px;
        overflow-y: scroll;
        .item {
            height: 40px;
            line-height: 40px;
            margin: 5px 0;
            padding: 10px 0 10px 20px;
            img {
                float: left;
                width: 40px;
                height: 40px;
                border-radius: 50%;
            }
            span {
                margin-left: 10px;
            }
        }
        .active {
            background: rgb(236, 236, 236);
        }
    }
    .user-list::-webkit-scrollbar {
        width: 5px;
    }
    .user-list::-webkit-scrollbar-track {
        background-color: #fff;
    }
    .user-list::-webkit-scrollbar-thumb {
        background-color: #cecece;
    }
    .content {
        height: 425px;
        overflow-y: scroll;
        border-left: 1px solid #cecece;
        img {
            width: 40px;
            height: 40px;
            border-radius: 50%;
        }
        p {
            margin: 0;
        }
        .float-left {
            float: left;
        }
        .float-right {
            float: right;
        }
        .text-right {
            text-align: right;
        }
        .item {
            margin: 10px 0;
            img {
                width: 30px;
                height: 30px;
                margin: 0 10px;
                border-radius: 50%;
            }
            .con {
                span {
                    font-weight: 100;
                    font-family: MicroSoft Yahei;
                    color: rgb(175, 174, 174);
                }
            }
        }
    }
    .content::-webkit-scrollbar {
        width: 5px;
        position:relative;
    }
    .content::-webkit-scrollbar-track {
        background-color: #fff;
    }
    .content::-webkit-scrollbar-thumb {
        background-color: #cecece;
    }
    .message {
        background: #ddd;
    }
}

</style>

