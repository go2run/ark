<template>
    <el-row class="host-page">
        <el-row class="host-option-panel">
            <el-col :span="18">
                <el-button class="host-option__delete" icon="delete" @click="deleteSelectHost">删除选中</el-button>
                <el-button class="host-option__add" icon="plus">添加主机</el-button>
                <el-button class="host-option__add" @click="refreshHostList"><i class="fa fa-repeat" style="padding-right: 8px"  ></i>刷新页面</el-button>
            </el-col>
            <el-col :span="6">
                <el-input placeholder="请输入内容">
                    <template slot="append"><i class="host-option__search el-icon-search"></i></template>
                </el-input>
            </el-col>
        </el-row>

        <el-row class="host-list-panel">
            <el-row class="host-titile">
                <el-col :span="1" class="host-title__chekbox"><template><el-checkbox v-model="chckeAllStatus"  @change='selectAll'></el-checkbox></template></el-col>
                <el-col :span="3" class="host-title__host-name">主机名</el-col>
                <el-col :span="3" class="host-title__host-ip">主机IP地址</el-col>
                <el-col :span="3" class="host-title__host-cpuusage">CPU使用率</el-col>
                <el-col :span="3" class="host-title__host-memusage">内存使用率</el-col>
                <el-col :span="3" class="host-title__host-memusage">内存大小</el-col>
                <el-col :span="3" class="host-title__host-memusage">磁盘大小</el-col>
                <el-col :span="3" class="host-title__host-memusage">操作</el-col>
                <el-col :span="2" class="host-title__status">状态</i></el-col>
            </el-row>
            <template v-for="(hostItem, index) in hostlist" >
                <el-row class="host-item">
                    <el-col :span="1" class="host-item__chekbox"><template><el-checkbox v-model="hostItem.checked"></el-checkbox></template></el-col>
                    <el-col :span="3" class="host-item__host-name">{{hostItem.hostName}}</el-col>
                    <el-col :span="3" class="host-item__host-ip">{{hostItem.hostIp}}</el-col>
                    <el-col :span="3" class="host-item__host-cpuusage">{{hostItem.hostCpuusage}}%</el-col>
                    <el-col :span="3" class="host-item__host-memusage">{{hostItem.hostMemusage}}%</el-col>
                    <el-col :span="3" class="host-item__host-memsize">{{hostItem.hostMemSize}}GB</el-col>
                    <el-col :span="3" class="host-item__host-disksize">{{hostItem.hostDiskSize}}G</el-col>
                    <el-col :span="3" class="host-item__host-option">
                        <el-row>
                            <el-col :span="8" class="edit"><i class="el-icon-edit"></i></el-col>
                            <el-col :span="8" class="refresh"><i class="fa fa-repeat"></i></el-col>
                            <el-col :span="8" class="delete"><i class="el-icon-delete2" @click="deleteOneHost(hostItem)"></i></el-col>
                        </el-row>
                    </el-col>
                    <el-col :span="2" class="host-item__status"><i class="fa fa-circle" aria-hidden="true"></i></el-col>
                </el-row>
            </template>
            <el-row class="host-item-add">
                <el-col :span="24"><i class="host-item-add__icon el-icon-plus"></i></el-col>
            </el-row>
        </el-row>

    </el-row>
</template>

<script>
    import swal from 'sweetalert2'
    export default {
        name: 'HostList',
        data() {
            return {
                msg: 'HostList',
                hostlist: [],
                swal
            }
        },
        computed: {
            chckeAllStatus() {
                if (this.hostlist.length === 0) {
                    return false;
                }
                return this.hostlist.every((host) => { return host.checked === true });
            }
        },
        methods: {
            selectAll() {
                console.log('change' + this.chckeAllStatus);
                let oldChekStatus = this.chckeAllStatus;
                this.hostlist.forEach((host) => {
                    host.checked = !oldChekStatus;
                });
            },
            deleteOneHost(host) {
                this.swal({
                    title: '警告',
                    text: '是否确定删除\'' + host.hostName + '\'?',
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonText: '删除',
                    cancelButtonText: '取消'
                }).then(() => {
                    let position = -1;
                    for (let i = 0; i < this.hostlist.length; i++) {
                        if (this.hostlist[i].id === host.id) {
                            position = i;
                            break;
                        }
                    }

                    if (position !== -1) {
                        this.hostlist.splice(position, 1);
                    }

                    this.swal(
                        '删除成功!',
                        '已删除\'' + host.hostName + '\'.',
                        'success'
                    )
                }, (dismiss) => {
                    console.log(dismiss);
                });
            },
            deleteSelectHost() {
                this.swal({
                    title: '警告',
                    text: '是否确定删除所选的主机?',
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonText: '删除',
                    cancelButtonText: '取消'
                }).then(() => {
                    this.hostlist = this.hostlist.filter((host) => {
                        return host.checked === false;
                    })
                    this.swal(
                            '删除成功!',
                            '已删除所选主机.',
                            'success'
                        )
                }, (dismiss) => {
                    console.log(dismiss);
                });
            },
            refreshHostList() {
                console.log('go');
                this.$router.replace('/cie/device/hostList?_=' + (new Date().getTime()));
            }
        },
        mounted() {
            let hosts = [
                {id: 1, checked: false, hostName: '主机1', hostIp: '10.71.171.91', hostCpuusage: 20, hostMemusage: 20, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 2, checked: false, hostName: '主机2', hostIp: '10.71.171.91', hostCpuusage: 80, hostMemusage: 2, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 3, checked: false, hostName: '主机3', hostIp: '10.71.171.92', hostCpuusage: 50, hostMemusage: 20, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 4, checked: false, hostName: '主机4', hostIp: '10.71.171.93', hostCpuusage: 80, hostMemusage: 50, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 5, checked: false, hostName: '主机5', hostIp: '10.71.171.94', hostCpuusage: 60, hostMemusage: 40, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 6, checked: false, hostName: '主机6', hostIp: '10.71.171.95', hostCpuusage: 80, hostMemusage: 5, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 7, checked: false, hostName: '主机7', hostIp: '10.71.171.96', hostCpuusage: 82, hostMemusage: 20, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 8, checked: false, hostName: '主机8', hostIp: '10.71.171.97', hostCpuusage: 90, hostMemusage: 26, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 9, checked: false, hostName: '主机9', hostIp: '10.71.171.98', hostCpuusage: 20, hostMemusage: 22, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 10, checked: false, hostName: '主机10', hostIp: '10.71.171.98', hostCpuusage: 30, hostMemusage: 21, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 11, checked: false, hostName: '主机11', hostIp: '10.71.171.11', hostCpuusage: 50, hostMemusage: 20.9, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 12, checked: false, hostName: '主机12', hostIp: '10.71.171.21', hostCpuusage: 60, hostMemusage: 20, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 13, checked: false, hostName: '主机13', hostIp: '10.71.171.31', hostCpuusage: 82, hostMemusage: 20, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 14, checked: false, hostName: '主机14', hostIp: '10.71.171.41', hostCpuusage: 40, hostMemusage: 20, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0},
                {id: 15, checked: false, hostName: '主机15', hostIp: '10.71.171.51', hostCpuusage: 1, hostMemusage: 20, hostMemSize: 10, hostDiskSize: 100, hostStatus: 0}
            ]
            this.hostlist = hosts;
        }
    }
</script>

<style lang="less">
.host-page {
    .host-option-panel {
        height: 80px;
        padding-top: 20px;
        padding-left: 20px;
        padding-right: 20px;
        .host-option__search {
            &:hover {
                cursor: pointer;
                color: #13CE66;
            }
        }
    }

    .host-list-panel {
        height: 80px;
        line-height: 80px;
        text-align: center;
        .host-titile {
            font-weight: bold;
            background-color: #E5E9F2;
            border-bottom: 1px solid #D3DCE6;
        }
        .host-item-add {
            border-bottom: 1px solid #f1f2f7;
            font-size: 26px;
            color: #8492A6;
            .host-item-add__icon {
                &:hover {
                    transform: scale(1.2, 1.2);
                    cursor: pointer;
                    color: #13CE66;
                }
            }
        }
        .host-item {
            border-bottom: 1px solid #f1f2f7;
            &:hover {
                background-color: #F9FAFC;
            }

            .host-item__status {
                text-align: center;
                color: #13CE66;
            }
            .host-item__host-option {
                text-align: center;
                .edit {
                    color: #8492A6;
                    &:hover {
                        color: #20A0FF;
                        cursor: pointer;
                    }
                }
                .refresh {
                    color: #8492A6;
                    &:hover {
                        color: #13CE66;
                        cursor: pointer;
                    }
                }
                .delete {
                    color: #8492A6;
                    &:hover {
                        color: #FF4949;
                        cursor: pointer;
                    }
                }
            }
        }  
    }
}
</style>
