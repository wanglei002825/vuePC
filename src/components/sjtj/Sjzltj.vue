<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-calendar"></i> 数据统计
                </el-breadcrumb-item>
                <el-breadcrumb-item>数据质量统计</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <!-- 树形下拉框  -->
                <template>
                    <el-form :inline="true" :v-model="form" class="demo-form-inline" size="mini" >
                        <el-row>
                            <el-col :span='24'>
                                <el-form-item ref="treeParent" label="办理单位" >
                                    <el-input
                                        placeholder="请选择办理单位"
                                        class="width-200 selectTree-input mr10"
                                        auto-complete="off"
                                        v-model="form.banldw"
                                        @click.native="changeSelectTree($event)">
                                    </el-input>
                                    <div ref="orgtree" class="width-200 selectTree-input objectTree" style="display:none;">
                                        <el-tree v-show="isShowSelect"
                                            empty-text="暂无数据"
                                            :highlight-current = true
                                            :default-expand-all = false
                                            :expand-on-click-node="true"
                                            :data="data"
                                            :filter-node-method="filterNode"
                                            @node-click="selectClassfy"
                                            class="objectTree2"
                                            ref="selectTree">
                                        </el-tree>
                                    </div>
                                </el-form-item>
                                <el-form-item label="事项类型">
                                    <el-select v-model="form.shixlx" :clearable="true" placeholder="事项类型" class="handle-select mr10">
                                        <el-option key="1" label="行政许可" value="行政许可"></el-option>
                                        <el-option key="2" label="行政确认" value="行政确认"></el-option>
                                        <el-option key="3" label="其他" value="其他"></el-option>
                                    </el-select>
                                </el-form-item>
                                <el-form-item label="业务类型">
                                    <el-select v-model="form.yewlx" :clearable="true" placeholder="业务类型" class="handle-select mr10">
                                        <el-option key="1" label="公路管理" value="公路管理"></el-option>
                                        <el-option key="2" label="运输管理" value="运输管理"></el-option>
                                        <el-option key="3" label="航道管理" value="航道管理"></el-option>
                                        <el-option key="4" label="港口管理" value="港口管理"></el-option>
                                        <el-option key="5" label="建设管理" value="建设管理"></el-option>
                                        <el-option key="6" label="地方海事" value="地方海事"></el-option>
                                    </el-select>
                                </el-form-item>
                                <el-form-item label="受理日期">
                                    <el-col :span="11">
                                        <el-date-picker
                                            type="date"
                                            placeholder="开始日期"
                                            v-model="form.date1"
                                            value-format="yyyy-MM-dd"
                                            style="width: 100%;"
                                        ></el-date-picker>
                                    </el-col>
                                    <el-col class="line" :span="2" style="padding-left:13px;">-</el-col>
                                    <el-col :span="11">
                                        <el-date-picker
                                            type="date"
                                            placeholder="结束日期"
                                            v-model="form.date2"
                                            value-format="yyyy-MM-dd"
                                            style="width: 100%;"
                                        ></el-date-picker>
                                    </el-col>
                                </el-form-item>
                                <el-button type="primary" icon="el-icon-s-data" @click="handleTongj">统计</el-button>
                            </el-col>
                        </el-row>
                    </el-form>
                </template>
            </div>
            <el-row :gutter="20" class="mgb20">
                <el-col :span="12">
                    <el-card shadow="hover" :body-style="{padding: '0px'}" class="cardBg">
                        <div id="chartPie1" style="width:100%; height:250px;"></div>
                        <div class="grid-content-chart grid-con-3">
                            <div class="grid-cont-right">
                                <div class="grid-num grid-num-blue">525</div>
                                <div class="grid-num-bottom">当月</div>
                            </div>
                            <div class="grid-cont-right">
                                <div class="grid-num grid-num-green">96.02%</div>
                                <div class="grid-num-bottom">环比</div>
                            </div>
                        </div>
                    </el-card>
                </el-col>
                <el-col :span="12">
                    <el-card shadow="hover" :body-style="{padding: '0px'}" class="cardBg">
                        <div id="chartBar1" style="width:100%; height:320px;"></div>
                    </el-card>
                </el-col>
            </el-row>
            <el-row :gutter="20" class="mgb20">
                <el-col :span="12">
                    <el-card shadow="hover" :body-style="{padding: '0px'}" class="cardBg">
                        <div id="chartLine1" style="width:100%; height:320px;"></div>
                    </el-card>
                </el-col>
                <el-col :span="12">
                    <el-card shadow="hover" :body-style="{padding: '0px'}" class="cardBg">
                        <div id="chartBar2" style="width:100%; height:320px;"></div>
                    </el-card>
                </el-col>
            </el-row>
            <el-row :gutter="20" class="mgb20">
                <el-col :span="12">
                    <el-card shadow="hover" :body-style="{padding: '0px'}" class="cardBg">
                        <div id="chartPie2" style="width:100%; height:320px;"></div>
                    </el-card>
                </el-col>
                <el-col :span="12">
                    <el-card shadow="hover" :body-style="{padding: '0px'}" class="cardBg">
                        <div id="chartBar3" style="width:100%; height:320px;"></div>
                    </el-card>
                </el-col>
            </el-row>
        </div>
    </div>
</template>

<script>
    import echarts from 'echarts'

    export default {
        data() {
            return {
                data: [{
                    label: '江苏省交通运输厅',
                    children: [{
                        label: '江苏省交通运输厅办公室',
                        children: []
                    },{
                        label: '江苏省交通运输厅安全处',
                        children: []
                    },{
                        label: '江苏省交通运输厅运管局',
                        children: []
                    },{
                        label: '江苏省交通运输综合行政执法监督局',
                        children: [{
                            label: '江苏省交通运输综合行政执法监督局综合处',
                            children:[]
                        },{
                            label: '江苏省交通运输综合行政执法监督局道路执法监督局',
                            children:[]
                        },{
                            label: '江苏省交通运输综合行政执法监督局水上执法监督局',
                            children:[]
                        },{
                            label: '江苏省交通运输综合行政执法监督局交通工程质量执法监督局',
                            children:[]
                        }]
                    },{
                        label: '江苏省交通运输厅公路事业发展中心',
                        children: []
                    },{
                        label: '江苏省交通运输厅港航事业发展中心',
                        children: []
                    },{
                        label: '江苏省交通通信信息中心',
                        children: []
                    },{
                        label: '南京市交通运输局',
                        children: []
                    },{
                        label: '南京市公路管理处',
                        children: []
                    },{
                        label: '南京市公路运输管理处',
                        children: []
                    },{
                        label: '南京市地方海事局',
                        children: []
                    },{
                        label: '南京市航道管理处',
                        children: []
                    },{
                        label: '南京市交通工程质量监督站',
                        children: []
                    },{
                        label: '南京市港口管理局',
                        children: []
                    },{
                        label: '南京市地方海事局',
                        children: []
                    },{
                        label: '南京市航道管理处',
                        children: []
                    },{
                        label: '南京市交通工程质量监督站',
                        children: []
                    },{
                        label: '南京市港口管理局',
                        children: []
                    },{
                        label: '南京市城市道路管理中心',
                        children: []
                    },{
                        label: '南京市客运交通管理处',
                        children: []
                    },{
                        label: '南京市汽车维修行业管理处',
                        children: []
                    }]
                }],
                isShowSelect:false,
                form:{banldw:''},
                watch: {
                    form: {
                        handler(form){
                            if(this.isShowSelect){
                                this.$refs.selectTree.filter(form.banldw);
                            }
                        },
                        deep: true,//深度监听，重要
                    },
                },
                chartPie1: null,
                chartBar1: null,
                chartLine1: null,
                chartBar2: null,
                chartPie2: null,
                chartBar3: null
            }
        },
        methods: {
            selectClassfy(data) {
                this.form.banldw=data.label;
                this.$refs.orgtree.style.display= 'none';
                this.isShowSelect=false;
            },
            changeSelectTree(e){
                if(this.isShowSelect){
                    this.$refs.orgtree.style.display= 'none';
                    this.isShowSelect = false;
                }else{
                    this.$refs.orgtree.style.display= 'block';
                    this.isShowSelect = true;
                }
                var that = this;
    　　　　　　　//第一种方式:点击其他区域, 消失树
                document.onclick=function(){
                    that.$refs.orgtree.style.display= 'none';
                    that.isShowSelect = false;
    　　　　　　 }
                e.stopPropagation();//阻止冒泡

    　　　　　　//离开区域的时候树消失
                this.$refs.orgtree.$el.onmouseleave = function () {
                    that.$refs.orgtree.style.display= 'none';
                    that.isShowSelect = false;
                }

                this.$refs.treeParent.$el.onmouseleave = function () {
                    that.$refs.orgtree.style.display= 'none';
                    that.isShowSelect = false;
                }
            },
            // 选择器的树节点
            filterNode(value, data) {
                if (!value) return true;
                return data.label.indexOf(value) !== -1;
            },
            drawBarChart1() {
                this.chartBar1 = echarts.init(document.getElementById('chartBar1'),'dark');
                this.chartBar1.setOption({
                    title: {
                        padding:[10,10],
                        text: '各地区办件数据内容报警情况',
                    },
                    tooltip: {},
                    grid: {
                        top:'25%',
                        left: '10%',
                        right: '10%',
                        bottom: '15%'
                    },
                    color:['#40adce','#b5640b','#14825a','#aaaa13','#959987', '#79a583', '#8B636C', '#6f2787'],
                    xAxis: {
                        name: '地区',
                        type: 'category',
                            //坐标轴斜着显示
                            axisLabel: {  
                            interval:0,  
                            rotate:30  
                        },
                        data: ['南京', '无锡', '徐州', '常州', '苏州', '南通', '连云港', '淮安', '盐城', '扬州', '镇江', '泰州', '宿迁']
                    },
                    yAxis: {
                        name: '报警数（次）',
                    },
                    series: [{
                        name: '报警数',
                        type: 'bar',
                        barWidth: 20,
                        data: [30, 19, 29, 8, 12, 62, 73, 23, 21, 12, 14, 27, 3]
                    }]
                });
            },
            drawLineChart1() {
                this.chartLine1 = echarts.init(document.getElementById('chartLine1'),'dark');
                this.chartLine1.setOption({
                    title: {
                        padding:[10,10],
                        text: '办件数据时效报警情况',
                    },
                    tooltip: {
                        trigger: 'axis'
                    },
                    grid: {
                        top:'25%',
                        left: '5%',
                        right: '12%',
                        bottom: '5%',
                        containLabel: true
                    },
                    color:['#40adce','#b5640b','#14825a','#aaaa13','#959987'],
                    xAxis: {
                        name: '月度',
                        type: 'category',
                        boundaryGap: false,
                        data: ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12"]
                    },
                    yAxis: {
                        name: '报警数（次）',
                        type: 'value'
                    },
                    series: [
                        {
                            name: '报警数',
                            type: 'line',
                            data: [21, 6, 12, 21, 30, 8, 10, 13, 18, 22, 25, 29]
                        }
                    ]
                });
            },
            drawPieChart1() {
                this.chartPie1 = echarts.init(document.getElementById('chartPie1'),'dark');
                this.chartPie1.setOption({
                    title: {
                        padding:[10,10],
                        text: '办件流程完整预报警情况',
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        x: 'right',
                        y: 'top',
                        top: '25%',
                        left: '70%',
                        data: ['高速公路', '普通公路', '运输管理', '港口管理', '航道管理', '地方海事', '建设管理']
                    },
                    series: [
                        {
                            name: '办件流程完整预报警情况',
                            type: 'pie',
                            radius: '80%',
                            center: ['35%', '60%'],
                            avoidLabelOverlap: false,
                            label : {
                                normal: {
                                    show: false,
                                },
                                labelLine : {
                                    show: false,
                                },
                            },
                            color:['#40adce','#b5640b','#14825a','#aaaa13','#959987', '#79a583', '#8B636C', '#6f2787'],
                            data: [
                                { value: 116, name: '高速公路' },
                                { value: 63, name: '普通公路' },
                                { value: 109, name: '运输管理' },
                                { value: 79, name: '港口管理' },
                                { value: 23, name: '航道管理' },
                                { value: 81, name: '地方海事' },
                                { value: 52, name: '建设管理' }
                            ],
                            itemStyle: {
                                emphasis: {
                                    shadowBlur: 10,
                                    shadowOffsetX: 0,
                                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                                }
                            }
                        }
                    ]
                });
            },
            drawBarChart2() {
                this.chartBar2 = echarts.init(document.getElementById('chartBar2'),'dark');
                this.chartBar2.setOption({
                   title: {
                        padding:[10,10],
                        text: '各地区双公示数据时效报警情况',
                    },
                    tooltip: {},
                    grid: {
                        top:'25%',
                        left: '10%',
                        right: '10%',
                        bottom: '12%'
                    },
                    color:['#40adce','#b5640b','#14825a','#aaaa13','#959987', '#79a583', '#8B636C', '#6f2787'],
                    xAxis: {
                        name: '地区',
                        type: 'category',
                            //坐标轴斜着显示
                            axisLabel: {  
                            interval:0,  
                            rotate:30  
                        },
                        data: ['南京', '无锡', '徐州', '常州', '苏州', '南通', '连云港', '淮安', '盐城', '扬州', '镇江', '泰州', '宿迁']
                    },
                    yAxis: {
                        name: '报警数（次）',
                    },
                    series: [{
                        name: '报警数',
                        type: 'bar',
                        barWidth: 20,
                        data: [96, 83, 72, 91, 86, 62, 16, 52, 69, 49, 26, 33, 31]
                    }]
                });
            },
            drawPieChart2(){
                this.chartPie2 = echarts.init(document.getElementById('chartPie2'),'dark');
                this.chartPie2.setOption({
                     title: {
                        padding:[10,10],
                        text: '双公示数据准确率情况',
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        x: 'right',
                        y: 'top',
                        top: '20%',
                        left: '70%',
                        data: ['高速公路', '普通公路', '运输管理', '港口管理', '航道管理', '地方海事', '建设管理']
                    },
                    series: [
                        {
                            name: '双公示数据准确率情况',
                            type: 'pie',
                            radius: '70%',
                            center: ['35%', '50%'],
                            avoidLabelOverlap: false,
                            label : {
                                normal: {
                                    show: false,
                                },
                                labelLine : {
                                    show: false,
                                },
                            },
                            color:['#40adce','#b5640b','#14825a','#aaaa13','#959987', '#79a583', '#8B636C', '#6f2787'],
                            data: [
                                { value: 280, name: '高速公路' },
                                { value: 179, name: '普通公路' },
                                { value: 290, name: '运输管理' },
                                { value: 179, name: '港口管理' },
                                { value: 423, name: '航道管理' },
                                { value: 481, name: '地方海事' },
                                { value: 352, name: '建设管理' }
                            ],
                            itemStyle: {
                                emphasis: {
                                    shadowBlur: 10,
                                    shadowOffsetX: 0,
                                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                                }
                            }
                        }
                    ]
                });
            },
            drawBarChart3() {
                this.chartBar3 = echarts.init(document.getElementById('chartBar3'),'dark');
                this.chartBar3.setOption({
                   title: {
                        padding:[10,10],
                        text: '各地区办件数据时效预报警情况',
                    },
                    tooltip: {},
                    grid: {
                        top:'25%',
                        left: '10%',
                        right: '10%',
                        bottom: '12%'
                    },
                    color:['#40adce','#b5640b','#14825a','#aaaa13','#959987', '#79a583', '#8B636C', '#6f2787'],
                    xAxis: {
                        name: '地区',
                        type: 'category',
                            //坐标轴斜着显示
                            axisLabel: {  
                            interval:0,  
                            rotate:30  
                        },
                        data: ['南京', '无锡', '徐州', '常州', '苏州', '南通', '连云港', '淮安', '盐城', '扬州', '镇江', '泰州', '宿迁']
                    },
                    yAxis: {
                        name: '报警数（次）',
                    },
                    series: [{
                        name: '报警数',
                        type: 'bar',
                        barWidth: 20,
                        data: [46, 53, 72, 31, 76, 62, 86, 92, 69, 121, 46, 33, 51]
                    }]
                });
            },
            drawCharts() {
                this.drawPieChart1()
                this.drawBarChart1()
                this.drawLineChart1()
                this.drawBarChart2()
                this.drawPieChart2()
                this.drawBarChart3()
            },
            // 触发统计按钮
            handleTongj() {
                
            }
        },
        mounted: function () {
            this.drawCharts()
        },
        updated: function () {
            this.drawCharts()
        }
    }
</script>

<style scoped>
    .mgb20 {
        margin-bottom: 20px;
    }
    .cardBg {
        background-color:#FBFBFB;
    }

    /*下拉框选择树*/
    .objectTree {
        margin-left:0px;
        position: absolute;
        overflow-x: auto;
        overflow-y: scroll;
        height: 200px;
        border: 1px solid #ddd;
        line-height: normal; 
        z-index: 204;
        background-color: #fff;
    }

    .treeShow {
        display: none;
    }

    .objectTree2 {
        margin-left:0px;
        position: absolute;
        line-height: normal; 
        z-index: 204;
        width: 450px;
    }

    .width-200{
        width: 200px;
    }

    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 125px;
    }

    .handle-input {
        width: 200px;
        display: inline-block;
    }

    .mr10 {
        margin-right: 10px;
    }

    .grid-content-chart {
        display: flex;
        align-items: center;
        height: 70px;
    }

    .grid-cont-right {
        flex: 1;
        text-align: center;
        font-size: 17px;
        color: #999;
    }

    .grid-num {
        font-size: 26px;
        font-weight: bold;
    }
    .grid-num-bottom {
        font-weight: bold;
        color:gray;
    }

    .grid-num-blue {
        color:  rgb(45, 140, 240);
    }

    .grid-num-green {
        color: rgb(100, 213, 114);
    }

    .grid-num-red {
        color: rgb(242, 94, 67);
    }

    .grid-num-yellow {
        color:#E6A23C;
    }
</style>
