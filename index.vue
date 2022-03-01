<template>
  <div>
    <el-container class="main">
      <div>
        <el-row :gutter="0">
          <el-col :xs="18" :sm="18" :md="18" :lg="10" :xl="10">
            <div class="frame_home">
              <div class="frame">
                <el-table
                  :data="tableData"
                  :header-cell-style="{ background: '#eee', color: '#606266' }"
                  :cell-style="{ 'text-align': 'left' }"
                  ref="singleTable"
                  :row-class-name="tableRowClassName"
                  row-key="id"
                >
                  <el-table-column type="index" label="Num" width="60">
                  </el-table-column>
                  <!-- <el-table-column
                    v-for="(item,key,val,index) in tableData[0]" :key="index"
                    >
                    <template slot="header">{{key}}</template>
                    <template slot-scpoe='scope'>{{tableData[scope.index.$index][key]}}</template>
                    </el-table-column> -->
                  <el-table-column prop="testName" label="Title" width="120px">
                  </el-table-column>
                  <el-table-column prop="result" label="Result" width="80px">
                  </el-table-column>

                  <el-table-column prop="count" label="Count">
                  </el-table-column>
                  <el-table-column prop="time" label="Time"> </el-table-column>

                  <!-- <el-table-column
                    prop="count"
                    label="Count"
                    >
                    </el-table-column>
                    <el-table-column
                    prop="time"
                    label="Time"
                    > -->
                  <!-- </el-table-column> -->
                </el-table>
              </div>
            </div>
          </el-col>

          <el-col :xs="24" :sm="24" :md="24" :lg="12" :xl="12">
            <div id="video" class="video_area" readonly :max-width="max_video">
              <video
                ref="videoCamera"
                style="width: 100%; height: 100%; object-fit: fill"
                poster="./logo.jpg"
                autoplay
              ></video>
              <a>视频显示区</a>
            </div>
          </el-col>
        </el-row>

        <el-row>
          <el-col :xs="18" :sm="18" :md="18" :lg="10" :xl="10">
            <div class="frame_home" style="margin-top: 20px">
              <div class="frame" style="height: 233px">
                <el-form>
                  <el-col :span="24" style="font-size: 20px">
                    <el-input
                      id="testName"
                      resize="none"
                      v-model="testFileName"
                      placeholder="用例文件名"
                      readonly
                    ></el-input>
                  </el-col>

                  <el-col :span="9" style="float: right">
                    <!-- <el-button  type="primary" :size="buttonSize" v-if=showForcase @click="openCase">测试设置</el-button> -->
                    <el-button
                      :size="buttonSize"
                      type="success"
                      v-show="video_state == 1"
                      @click="video_state = 2"
                      >仅失败保存视频</el-button
                    >
                    <el-button
                      :size="buttonSize"
                      type="danger"
                      v-show="video_state == 2"
                      @click="video_state = 3"
                      >不保存视频</el-button
                    >
                    <el-button
                      :size="buttonSize"
                      type="warning"
                      v-show="video_state == 3"
                      @click="video_state = 1"
                      >保存全部视频</el-button
                    >
                  </el-col>

                  <el-col :span="9" style="float: left; margin-left: 50px">
                    <input
                      type="file"
                      ref="file"
                      hidden
                      @change="fileChange($event)"
                    />
                    <el-button
                      :size="buttonSize"
                      type="primary"
                      icon="el-icon-edit"
                      @click="testcase_dialogVisible = true"
                      >用例设置</el-button
                    >
                  </el-col>

                  <el-col :span="10">
                    <el-input
                      v-model="run_num"
                      resize="none"
                      placeholder="运行数显示区"
                      :rows="3"
                      readonly
                    ></el-input>
                  </el-col>

                  <el-col :span="4" style="font-size: 30px; text-align: center">
                    <a>/</a>
                  </el-col>

                  <el-col :span="10">
                    <el-input
                      v-model="all_num"
                      resize="none"
                      placeholder="总数显示区"
                      :rows="3"
                      readonly
                    ></el-input>
                  </el-col>

                  <el-col :span="10">
                    <el-input
                      v-model="success_num"
                      resize="none"
                      placeholder="成功数显示区"
                      :rows="3"
                      readonly
                    ></el-input>
                  </el-col>
                  <el-col :span="4" style="font-size: 30px; text-align: center">
                    <a>:</a>
                  </el-col>
                  <el-col :span="10">
                    <el-input
                      v-model="fail_num"
                      resize="none"
                      placeholder="失败数显示区"
                      :rows="3"
                      readonly
                    ></el-input>
                  </el-col>
                </el-form>
              </div>
            </div>
          </el-col>

          <el-col :xs="24" :sm="24" :md="24" :lg="12" :xl="12">
            <div class="log_area">
              <div style="display: inline-block">
                <div id="settingBtn" v-show="!isRunning">
                  <el-button
                    style="margin-left: 10px"
                    :size="buttonSize"
                    type="primary"
                    @click="monitor_dialog = true"
                    >显示器设置</el-button
                  >
                  <el-button
                    :size="buttonSize"
                    type="primary"
                    @click="device_dialog = true"
                    >USB设备选择</el-button
                  >
                  <el-button
                    :size="buttonSize"
                    type="primary"
                    @click="dialogVisible = true"
                    >串口调试</el-button
                  >
                  <el-button
                    v-show="force_restart_state == 0"
                    :size="buttonSize"
                    type="primary"
                    @click="force_restart_state = 1"
                    >无响应不重启</el-button
                  >
                  <el-button
                    v-show="force_restart_state == 1"
                    :size="buttonSize"
                    type="primary"
                    @click="force_restart_state = 2"
                    >无响应重启TP</el-button
                  >
                  <el-button
                    v-show="force_restart_state == 2"
                    :size="buttonSize"
                    type="primary"
                    @click="force_restart_state = 0"
                    >无响应重启SP</el-button
                  >
                  <el-button
                    v-show="error_stop_state == 0"
                    :size="buttonSize"
                    type="primary"
                    @click="error_stop_state = 1"
                    >失败停止测试</el-button
                  >
                  <el-button
                    v-show="error_stop_state == 1"
                    :size="buttonSize"
                    type="primary"
                    @click="error_stop_state = 0"
                    >失败不停止</el-button
                  >
                  <el-button
                    :size="buttonSize"
                    type="primary"
                    @click="monitor_test()"
                    >检测显示器</el-button
                  >
                  <!-- <el-button
                    :size="buttonSize"
                    type="primary"
                    @click="stopLive()"
                    >关闭摄像头</el-button
                  >
                  <el-button
                    :size="buttonSize"
                    type="primary"
                    @click="
                      get_screenshot(
                        'D:\\0105\\serve\\serve\\static\\Log\\1.jpg'
                      )
                    "
                    >下载</el-button
                  >
                  <el-button
                    :size="buttonSize"
                    type="primary"
                    @click="
                      upload_log(
                        'D:\\0105\\serve\\serve\\static\\Log\\0.MP4',
                        3
                      )
                    "
                    >上传log文件</el-button
                  > -->
                </div>
                <div style="width: 280px" v-show="isRunning">
                  <el-input
                    id="log_data"
                    v-model="log_data"
                    type="textarea"
                    resize="none"
                    placeholder="Log显示区"
                    :rows="10"
                    readonly
                  ></el-input>
                </div>
              </div>

              <div style="display: inline-block">
                <div class="ipAddr">
                  <ul class="ipAdress" id="ip">
                    <li v-for="(item, index) in ipAddress" :key="index">
                      <input
                        type="text"
                        @input="checkIpVal(item, index)"
                        @keyup="turnIpPOS(item, index, $event)"
                        v-model="item.value"
                        ref="ipInput"
                        @blur="setDefaultVal(item)"
                      />
                      <div>:</div>
                    </li>
                  </ul>
                  <div style="text-align: center; margin-top: 10px">
                    <el-button
                      :size="buttonSize"
                      :disabled="!isRunning"
                      type="primary"
                      @click="stop_test()"
                      >暂停测试</el-button
                    >
                    <el-button
                      :size="buttonSize"
                      type="primary"
                      @click="get_client_status()"
                      >检查连接</el-button
                    >
                  </div>
                  <div style="text-align: center; margin-top: 10px">
                    <el-button
                      v-show="START"
                      :size="buttonSize"
                      type="primary"
                      style="
                        width: 200px;
                        height: 80px;
                        font-size: 30px;
                        color: black;
                        background-color: #01b1ad;
                        border-color: #01b1ad;
                      "
                      @click="
                        booking_dialog = true;
                        get_user_book();
                      "
                      >START</el-button
                    >
                    <template>
                      <el-popconfirm
                        title="确定停止?"
                        @onConfirm="stop_fun()"
                        @onCancel="cancelEvent()"
                      >
                        <template #reference>
                          <el-button
                            v-show="!START"
                            :size="buttonSize"
                            type="primary"
                            style="
                              width: 200px;
                              height: 80px;
                              font-size: 30px;
                              color: black;
                              background-color: #efe0e3;
                              border-color: #efe0e3;
                              margin-left: -5px;
                            "
                            >STOP</el-button
                          >
                        </template>
                      </el-popconfirm>
                    </template>
                  </div>
                </div>
              </div>
            </div>
          </el-col>
        </el-row>
      </div>
      <el-dialog
        title="设备选择"
        :visible.sync="device_dialog"
        width="60%"
        v-loading="loading"
      >
        <div>
          <el-table
            :data="deviceData"
            max-height="450"
            :key="Math.random()"
            stripe
            style="width: 100%; text-align: center"
            @selection-change="handleSelectionChange"
          >
            <!-- checkbox选中框 -->
            <el-table-column
              type="selection"
              width="55"
              :selectable="selectable"
            ></el-table-column>
            <el-table-column
              prop="DeviceDescription"
              label="设备描述"
              width="180"
            />
            <el-table-column prop="InstanceID" label="实例 ID" width="180" />
            <el-table-column prop="ClassName" label="类别" width="180" />
            <el-table-column
              prop="ManufacturerName"
              label="厂商信息"
              width="180"
            />
          </el-table>
          <div style="margin-top: 20px">
            <el-button
              :size="buttonSize"
              :disabled="auto_list_disable"
              @click="auto_list()"
              >自动筛选</el-button
            >
            <!-- <el-button :size="buttonSize" @click="none_check">全不选</el-button>
    <el-button :size="buttonSize" @click="all_check">全选</el-button>
    <el-button :size="buttonSize" @click="manaul_list">手动选择</el-button> -->
            <el-button
              :size="buttonSize"
              :disabled="auto_list_disable"
              @click="normal_list()"
              >手动选择</el-button
            >
            <el-button
              type="primary"
              style="float: right"
              :size="buttonSize"
              @click="save_data('usb')"
              >确定</el-button
            >
          </div>
        </div>
      </el-dialog>

      <el-dialog
        title="选择预约项"
        :visible.sync="booking_dialog"
        width="50%"
        v-loading="loading"
      >
        <el-form
          :model="ruleForm2"
          :rules="rules"
          ref="ruleForm2"
          label-width="100px"
          class="demo-ruleForm"
        >
          <el-form-item label="测试时间" required>
            <el-col :span="6">
              <el-date-picker
                type="date"
                placeholder="选择日期"
                v-model="ruleForm2.date1"
                value-format="yyyy-MM-dd"
                style="width: 100%"
              >
              </el-date-picker>
            </el-col>
            <el-col style="text-align: center" :span="2">-</el-col>
            <el-col :span="11">
              <el-time-picker
                placeholder="选择时间"
                v-model="ruleForm2.date2"
                style="width: 100%"
                value-format="HH:mm:ss"
              >
              </el-time-picker>
            </el-col>
          </el-form-item>

          <el-form-item label="测试机器" prop="machine">
            <el-radio-group v-model="ruleForm2.machine">
              <el-radio label="LAAT 1"></el-radio>
              <el-radio label="LAAT 2"></el-radio>
              <el-radio label="LAAT 3"></el-radio>
              <el-radio label="LAAT 4"></el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item>
            <el-table
              :data="bookingdata"
              max-height="450"
              :key="Math.random()"
              stripe
              style="width: 90%; text-align: left; margin-left: -70px"
              @selection-change="handleSelectionChange"
            >
              <!-- checkbox选中框 -->
              <el-table-column
                type="selection"
                width="55"
                :selectable="selectable"
              ></el-table-column>
              <el-table-column prop="testName" label="标题" width="180" />
              <el-table-column prop="testType" label="测试项目" width="180" />
            </el-table>
          </el-form-item>
          <el-form-item>
            <el-alert
              title="提交成功"
              type="success"
              v-show="success_alert"
              style="height: 30px; margin-bottom: 5px"
            >
            </el-alert>

            <el-button type="primary" @click="start_confrim('ruleForm2')"
              >立即开始</el-button
            >
            <el-button @click="resetForm('ruleForm2')">重置</el-button>
            <el-button @click="booking_dialog = false">取 消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>

      <el-dialog
        title="显示器设置"
        :visible.sync="monitor_dialog"
        width="50%"
        v-loading="loading"
      >
        <el-main style="">
          <el-form style="margin-top: -50px">
            <el-form-item style="display: inline">
              <h3>Display Infomation</h3>
              <div style="width: 300px">
                <span>Display Mode: </span>
                <el-input
                  id="pullDta"
                  v-model="DisplayModeData"
                  resize="none"
                  placeholder="屏幕模式显示区"
                  style="width: 150px; float: right"
                  :rows="1"
                  readonly
                ></el-input>
                <br />
                <span>Display Number: </span>
                <el-input
                  id="pullDta"
                  v-model="DisplayNumData"
                  resize="none"
                  placeholder="屏幕数量显示区"
                  style="width: 150px; float: right"
                  :rows="1"
                  readonly
                ></el-input>
              </div>
            </el-form-item>

            <el-form-item>
              <h3>Light Sensor</h3>
              <span>Sensor 1: </span>
              <div
                v-for="(sensor, index) in lightSensorArray"
                :key="index"
                style="display: inline"
              >
                <div style="display: inline" v-show="index % 2 != 0">
                  <div v-bind:class="sensorClassName(sensor)">/</div>
                </div>
              </div>
              <br />
              <div style="margin-top: 20px">
                <span>Sensor 2: </span>
                <div
                  v-for="(sensor, index) in lightSensorArray"
                  :key="index"
                  style="display: inline"
                >
                  <div style="display: inline" v-show="index % 2 == 0">
                    <div v-bind:class="sensorClassName(sensor)">/</div>
                  </div>
                </div>
              </div>
            </el-form-item>
            <el-form-item>
              <h3>Resolusion</h3>
              <div
                style="
                  width: 280px;
                  margin-top: 10px;
                  display: inline-block;
                  margin-left: 5px;
                "
                v-for="(i, index) in Display"
                :Key="index"
              >
                <h4>Monitor {{ index }}</h4>
                <span>Monitor Module: </span>
                <el-input
                  v-model="i['Monitor Module']"
                  resize="none"
                  placeholder="屏幕模式显示区"
                  style="width: 150px; float: right"
                  :rows="1"
                  readonly
                ></el-input>
                <br />
                <span>Resolution: </span>
                <el-input
                  v-model="i['Resolution']"
                  resize="none"
                  placeholder="屏幕数量显示区"
                  style="width: 150px; float: right"
                  :rows="1"
                  readonly
                ></el-input>
                <br />
                <span>Monitor Name: </span>
                <el-input
                  v-model="i['Monitor Name']"
                  resize="none"
                  placeholder="屏幕数量显示区"
                  style="width: 150px; float: right"
                  :rows="1"
                  readonly
                ></el-input>
              </div>
            </el-form-item>
            <el-form-item>
              <el-button :size="buttonSize" @click="monitor"
                >检测显示器</el-button
              >
              <el-button
                style="float: right"
                type="primary"
                :size="buttonSize"
                @click="save_data('monitor')"
                >保存</el-button
              >
            </el-form-item>
          </el-form>
        </el-main>
      </el-dialog>

      <el-dialog
        title="电机调试"
        :visible.sync="dialogVisible"
        width="70%"
        v-loading="loading"
      >
        <div>
          <el-form style="height: 450px; text-align: center">
            <el-row>
              <img
                style="width: 200px; height: 200px; margin-left: 5px"
                src="../../../static/plugtool_1.jpg"
              />
              <img
                style="width: 200px; height: 200px; margin-left: 5px"
                src="../../../static/plugtool_2.jpg"
              />
              <img
                style="width: 200px; height: 200px; margin-left: 5px"
                src="../../../static/plugtool_3.jpg"
              />
              <img
                style="width: 200px; height: 200px; margin-left: 5px"
                src="../../../static/plugtool_4.jpg"
              />
            </el-row>
            <el-row>
              <el-col :span="2" style="margin-top: 20px">
                <a>TOP:</a>
              </el-col>
              <el-col :span="3">
                <el-button
                  :size="buttonSize"
                  class="el-btn"
                  @click="cut_dis('Top')"
                  >-</el-button
                >
              </el-col>
              <el-col :span="5">
                <el-input
                  v-model="ca['Top_dis']"
                  style="margin: 10px 10px"
                  resize="none"
                  placeholder="Top Distance"
                  readonly
                ></el-input>
              </el-col>
              <el-col :span="3">
                <el-button
                  :size="buttonSize"
                  class="el-btn2"
                  @click="add_dis('Top')"
                  >+</el-button
                >
              </el-col>
              <el-col :span="4">
                <el-input
                  v-model="ca['Top_num']"
                  style="margin: 10px 15px"
                  resize="none"
                  placeholder="Top Num"
                ></el-input>
              </el-col>
              <el-col :span="6">
                <el-button
                  v-show="cd['Top']"
                  :size="buttonSize"
                  class="el-btn3"
                  @click="connect('Top')"
                  style="background-color: #4fc08d"
                  >CONNECTED</el-button
                >
                <el-button
                  v-show="!cd['Top']"
                  :size="buttonSize"
                  class="el-btn3"
                  @click="connect('Top')"
                  >DISCONNECT</el-button
                >
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="2" style="margin-top: 20px">
                <a>SIDE:</a>
              </el-col>
              <el-col :span="3">
                <el-button
                  :size="buttonSize"
                  class="el-btn"
                  @click="cut_dis('Side')"
                  >-</el-button
                >
              </el-col>
              <el-col :span="5">
                <el-input
                  v-model="this.ca['Side_dis']"
                  style="margin: 10px 10px"
                  resize="none"
                  placeholder="Side Distance"
                  readonly
                ></el-input>
              </el-col>
              <el-col :span="3">
                <el-button
                  :size="buttonSize"
                  class="el-btn2"
                  @click="add_dis('Side')"
                  >+</el-button
                >
              </el-col>
              <el-col :span="4">
                <el-input
                  v-model="ca['Side_num']"
                  style="margin: 10px 15px"
                  resize="none"
                  placeholder="Side Num"
                ></el-input>
              </el-col>
              <el-col :span="6">
                <el-button
                  v-show="cd['Side']"
                  :size="buttonSize"
                  class="el-btn3"
                  @click="connect('Side')"
                  style="background-color: #4fc08d"
                  >CONNECTED</el-button
                >
                <el-button
                  v-show="!cd['Side']"
                  :size="buttonSize"
                  class="el-btn3"
                  @click="connect('Side')"
                  >DISCONNECT</el-button
                >
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="2" style="margin-top: 20px">
                <a>TYPE-C:</a>
              </el-col>
              <el-col :span="3">
                <el-button
                  :size="buttonSize"
                  class="el-btn"
                  @click="cut_dis('TypeC')"
                  >-</el-button
                >
              </el-col>
              <el-col :span="5">
                <el-input
                  v-model="this.ca['TypeC_dis']"
                  style="margin: 10px 10px"
                  resize="none"
                  placeholder="Type-C Distance"
                  readonly
                ></el-input>
              </el-col>
              <el-col :span="3">
                <el-button
                  :size="buttonSize"
                  class="el-btn2"
                  @click="add_dis('TypeC')"
                  >+</el-button
                >
              </el-col>
              <el-col :span="4">
                <el-input
                  v-model="this.ca['TypeC_num']"
                  style="margin: 10px 15px"
                  resize="none"
                  placeholder="Type-C Num"
                ></el-input>
              </el-col>
              <el-col :span="6">
                <el-button
                  v-show="cd['TypeC']"
                  :size="buttonSize"
                  class="el-btn3"
                  @click="connect('TypeC')"
                  style="background-color: #4fc08d"
                  >CONNECTED</el-button
                >
                <el-button
                  v-show="!cd['TypeC']"
                  :size="buttonSize"
                  class="el-btn3"
                  @click="connect('TypeC')"
                  >DISCONNECT</el-button
                >
              </el-col>
            </el-row>
          </el-form>
        </div>
        <div style="margin-top: -50px">
          <el-select
            style="display: none"
            v-model="com"
            :disabled="isOpen"
            placeholder="请选择"
          >
            <el-option
              v-for="port in ports"
              :label="port.label"
              :value="port.value"
              :key="port.value"
            ></el-option>
          </el-select>

          <el-button
            :size="buttonSize"
            style="margin-left: 30px; display: none"
            @click="openCom()"
            >开启串口</el-button
          >
          <span>
            <a style="margin-left: 30px; font-size: 20px">按压电源按钮的时间</a>
          </span>
          <el-input
            v-model="powerButton"
            style="width: 100px; margin-left: 10px"
          ></el-input>

          <el-button
            type="primary"
            :size="buttonSize"
            style="float: right; margin-right: 30px"
            @click="save_data('serialPort')"
            >保存</el-button
          >
          <el-button
            type="primary"
            :size="buttonSize"
            style="float: right; margin-right: 30px"
            @click="readSerialPort()"
            >读取数据</el-button
          >
          <!-- <el-button
            type="primary"
            :size="buttonSize"
            style="float: right; margin-right: 30px"
            @click="initSerialPort()"
            >初始化串口</el-button
          > -->
          <!-- <el-button type="primary" :size="buttonSize" style="float:right;margin-right:30px" @click="reSetSerialport()">重置数据</el-button> -->
        </div>
      </el-dialog>

      <!-- <el-aside >
            <el-form >
               
                    <el-form-item style="text-align: right;padding-right:10px;">
                    <el-button :size="buttonSize"  @click="openCamera">开启摄像头</el-button>
                    <el-button :size="buttonSize"  @click="closeCamera">关闭摄像头</el-button>
                    <el-button :size="buttonSize"  @click="startRecording">开始录制视频</el-button>
                    <el-button :size="buttonSize"  @click="stopLive">停止录制视频</el-button>
                    <el-button :size="buttonSize"  @click="cmdTe">手动发送</el-button>
                    </el-form-item>
            </el-form>
        </el-aside> -->
    </el-container>

    <el-dialog
      title="测试用例"
      :visible.sync="testcase_dialogVisible"
      width="80%"
      v-loading="loading"
    >
      <div style="min-height: 500px">
        <div
          style="
            width: 45%;
            display: inline-block;
            border: 1px dashed skyblue;
            height: 470px;
            overflow: auto;
          "
        >
          <el-table
            class="testCase"
            height="468"
            highlight-current-row
            @current-change="handleCurrentChange"
            :data="CaseData.col"
            style="width: 100%"
          >
            <el-table-column type="index" label="Num" width="60">
            </el-table-column>
            <el-table-column label="CaseName">
              <template slot-scope="scope">
                <span v-if="scope.row.isSet">
                  <el-input size="mini" v-model="scope.row.CaseName"></el-input>
                </span>
                <span v-else>{{ scope.row.CaseName }}</span>
              </template>
            </el-table-column>
            <el-table-column label="count">
              <template slot-scope="scope">
                <span v-if="scope.row.isSet">
                  <el-input size="mini" v-model="scope.row.count"></el-input>
                </span>
                <span v-else>{{ scope.row.count }}</span>
              </template>
            </el-table-column>
            <!-- <el-table-column
      label="Result"
      prop="result">
    </el-table-column> -->
            <el-table-column align="right">
              <template slot="header" slot-scope="scope">
                <input
                  type="file"
                  ref="file"
                  hidden
                  @change="fileChange($event)"
                />
                <el-button
                  v-model="handleAdd"
                  size="mini"
                  type="success"
                  round
                  plain
                  @click="handleAdd(scope.$index, scope.row)"
                  >新增</el-button
                >
              </template>
              <template slot-scope="scope" style="display: inline">
                <el-button
                  size="mini"
                  type="primary"
                  round
                  plain
                  v-if="!scope.row.isSet"
                  @click="handleChange(scope.row, scope.$index, true)"
                  >编辑</el-button
                >
                <el-button
                  size="mini"
                  type="primary"
                  round
                  plain
                  v-else
                  @click="handleChange(scope.row, scope.$index, true)"
                  >保存</el-button
                >
                <el-button
                  size="mini"
                  type="danger"
                  round
                  plain
                  v-if="!scope.row.isSet"
                  @click="handleDelete(scope.$index, scope.row)"
                  >删除</el-button
                >
                <el-button
                  size="mini"
                  type="danger"
                  round
                  plain
                  v-else
                  @click="handleChange(scope.row, scope.$index, false)"
                  >取消</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </div>
        <div style="width: 10%; display: inline-block"></div>
        <div
          style="
            width: 45%;
            display: inline-block;
            border: 1px dashed skyblue;
            height: 470px;
            overflow: auto;
          "
        >
          <el-table
            :data="
              testData.col.filter(
                (data) =>
                  CaseAdd ||
                  data.testName.toLowerCase().includes(CaseAdd.toLowerCase())
              )
            "
            highlight-current-row
            class="testCase"
            height="468"
          >
            <el-table-column type="index" label="Num" width="60">
            </el-table-column>
            <el-table-column label="testName">
              <template slot-scope="scope">
                <span v-if="scope.row.isSet">
                  <el-select size="mini" v-model="scope.row.testName">
                    <el-option
                      v-for="item in testNameOptions"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    ></el-option>
                  </el-select>
                </span>
                <span v-else>{{ scope.row.testName }}</span>
              </template>
            </el-table-column>
            <el-table-column label="Time">
              <template slot-scope="scope">
                <span v-if="scope.row.isSet">
                  <el-input size="mini" v-model="scope.row.time"></el-input>
                </span>
                <span v-else>{{ scope.row.time }}</span>
              </template>
            </el-table-column>

            <el-table-column align="center" width="170px">
              <template slot="header" slot-scope="scope">
                <el-button
                  v-model="CaseAdd"
                  size="mini"
                  type="success"
                  round
                  plain
                  @click="CaseAdd(scope.$index, scope.row)"
                  >新增</el-button
                >
              </template>
              <template slot-scope="scope">
                <el-button
                  size="mini"
                  type="primary"
                  round
                  plain
                  v-if="!scope.row.isSet"
                  @click="valChange(scope.row, scope.$index, true)"
                  >编辑</el-button
                >
                <el-button
                  size="mini"
                  type="primary"
                  round
                  plain
                  v-else
                  @click="valChange(scope.row, scope.$index, true)"
                  >保存</el-button
                >
                <el-button
                  size="mini"
                  type="danger"
                  round
                  plain
                  v-if="!scope.row.isSet"
                  @click="CaseDelete(scope.$index, scope.row)"
                  >删除</el-button
                >
                <el-button
                  size="mini"
                  type="danger"
                  round
                  plain
                  v-else
                  @click="valChange(scope.row, scope.$index, false)"
                  >取消</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </div>
        <el-alert
          title="请选择Json文件"
          type="error"
          effect="dark"
          v-show="error_show"
          style="margin-bottom: 5px; min-width: 350px"
        >
        </el-alert>
        <div style="bottom: 5px">
          <el-button
            type="primary"
            :size="buttonSize"
            style="float: left; margin-right: 30px"
            @click="handleFileChange"
            >读取用例文件</el-button
          >

          <el-button
            type="primary"
            :size="buttonSize"
            style="float: left; margin-right: 30px"
            @click="save_to_file('TestCase')"
            >保存用例为文件</el-button
          >

          <el-button
            :size="buttonSize"
            style="float: right; margin-right: 30px"
            @click="testcase_dialogVisible = false"
            >取消</el-button
          >
          <el-button
            type="primary"
            :size="buttonSize"
            style="float: right; margin-right: 30px"
            @click="save_data('testCase')"
            >保存</el-button
          >
          <!-- <el-button type="primary" :size="buttonSize" style="float:right;margin-right:30px" @click="showTestCase">显示用例</el-button> -->
        </div>
      </div>
    </el-dialog>
    <el-dialog
      title="错误提示"
      :visible.sync="errorVisible"
      width="30%"
      v-loading="loading"
    >
      <span>{{ errormsg }}</span>
      <template #footer>
        <span class="dialog-footer">
          <el-button type="primary" @click="errorVisible = false"
            >确定</el-button
          >
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script>
import Vue from "vue";
import Sortable from "sortablejs";
import serialport from "../serialport/index.vue";
import { mapActions } from "vuex";
import { getBaseURL, setMachine } from "@/common/auth";
import { set_server_ip } from "@/api/login";
import axios from "axios";
import path from "path";
import { getUser, getUserName } from "../../common/auth";
// import { extend } from 'vue/types/umd';

export default {
  compoment: {
    serialport,
  },
  name: "Main",
  watch: {
    autoSendRate(val) {
      let _ts = this;
      if (val > 0) {
        _ts.autoSendData(_ts.autoSend);
      }
    },
    // pullData: 'log_change',// 值可以为methods的方法名,
    dialogVisible: "initSerialPort", //打开串口弹框时初始化串口
    video_state: "save_video_state", //更改视频保存状态时本地化储存
    error_stop_state: "error_stop", //更改错误停止状态时本地保存
    hasForce: "force_restart", //更改未响应重启状态时本地保存
    CaseData: "CaseChange", //更改用例时清空数据
    ruleForm2: "setMachine", //更改时本地保存测试机器名
    log_data: "log_set_height", //log变动时滚动到最下方
    // monitor_dialog:'do_light_detact',
    max_video() {
      return `${window.screen.avaiWidth * 0.45}px`;
    },
  },
  data() {
    return {
      firstRun: false,
      portIsOpen: false,
      SerialPortAvaliable: false,
      errorVisible:false,//错误提示弹框
      errormsg:'',//错误提示信息
      powerButton: 1, //电源按钮按压时间
      force_restart_state: 0, //失败重启数字状态
      error_stop_state: 0, //错误停止数字状态
      hasFail: false, //是否有失败测试项
      hasForce: false, //是否需要重启
      max_video: 430, //最大视频区域宽度
      START: true, //开始按钮显示状态
      isRunning: false, //当前运行状态
      isStanding:false,
      file_path: "", //Test case 文件路径
      dialogVisible: false,
      booking_dialog: false, //开始测试前预约选择弹框
      testcase_dialogVisible: false, //testcase 弹框
      auto_list_disable: false,
      loading: false, //加载状态
      showForcase: true,
      error_show: false, //错误提示弹框
      device_dialog: false, //设备选择弹框
      selectionData: [], //已选设备列表
      bookingdata: [], //用户预约列表数据
      lightSensorArray: [
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
      ], //光线感应数组
      clientIp: "", //客户端ip
      monitor_dialog: false, //显示器弹框
      DisplayModeData: "", //显示模式
      DisplayNumData: "", //显示器数量
      Display: {}, //显示器数据
      video_state: 3, //视频录制状态
      // top_dis:0,//顶部电机位移距离
      // top_num:100,//顶部电机增减距离
      // side_dis:0,//右侧电机位移距离
      // side_num:100,//右侧电机增减距离
      // typec_dis:0,//TypeC电机位移距离
      // typec_num:100,//TypeC电机增减距离
      ca: {
        //接口距离
        Top_dis: 0,
        Top_num: 100,
        Side_dis: 0,
        Side_num: 100,
        TypeC_dis: 0,
        TypeC_num: 100,
      },
      cd: {
        //接口状态
        Top: false,
        Side: false,
        TypeC: false,
      },
      buttonSize: "big", //按钮大小
      pullData: "", // 接收到的数据
      log_data: "",
      pushData: "", // 发送的数据
      options: {
        // 串口配置信息
        baudRate: 9600,
        parity: "none",
        dataBits: 8,
        stopBits: 1,
        autoOpen: false,
      },
      ports: [], // 本机串口列表
      com: "com4", //com口信息
      port: null, //串口实例
      isOpen: false,
      portResult: "",
      running_id:0,
      record_state: false, //录制状态
      mediaRecorder: null, //视频录制数据
      thisCancas: null,
      thisContext: null,
      thisVideo: null,
      liveVideo: null,
      old_log: "",
      ipAddress: [
        {
          value: "",
        },
        {
          value: "",
        },
        {
          value: "",
        },
        {
          value: "",
        },
      ], //ip地址数组
      CaseData: {
        sel: null, // 选中行
        col: [],
      },
      testData: {
        sel: null, // 选中行
        col: [],
      },
      isSelectItem: false,
      ruleForm2: {
        //开始弹框表单
        date1: "",
        date2: "",
        machine: "",
      },
      rules: {
        //判定规则
        date1: [
          {
            type: "date",
            required: true,
            message: "请选择日期",
            trigger: "change",
          },
        ],
        mechine: [
          { required: true, message: "请选择测试机器", trigger: "blur" },
        ],
      },
      globlaStatic: "",
      testFileName: "", //testcase 文件名
      tableData: [], //列表数组
      deviceData: [], //设备数组
      deviceData2: [],
      dataKey: true,
      success_alert: false, //成功提示
      search: "", //搜索绑定值
      current_case: [], //当前已选case
      all_num: 0,
      run_num: 0,
      success_num: 0,
      fail_num: 0,
      warnOptions: [
        {
          value: true,
          label: "是",
        },
        {
          value: false,
          label: "否",
        },
      ], //警告提示
      testNameOptions: [
        {
          value: "s3",
          label: "s3",
        },
        {
          value: "s4",
          label: "s4",
        },
        {
          value: "s5",
          label: "s5",
        },
        {
          value: "plug in",
          label: "plug in",
        },
        {
          value: "plug out",
          label: "plug out",
        },
        {
          value: "top button",
          label: "top button",
        },
        {
          value: "side button",
          label: "side button",
        },
        {
          value: "device check",
          label: "device check",
        },
        {
          value: "monitor check",
          label: "monitor check",
        },
        {
          value: "power check",
          label: "power check",
        },
      ], //testcase 选项
    };
  },

  methods: {
    ...mapActions(["addVisitedTabsView", "delVisitedTabsView"]),
    //带重试次数的axios封装函数
    r_axios(data) {
      // {
      //   url:'http://'+ip+':3000/',
      //   method:'get',
      //   data:{
      //     //  "testName":测试标题,
      //       "ip":ip,
      //   },
      //   headers:{
      //     'Content-type':'application/json'
      //   },
      //  try_count:10,
      //  try_time:10

      // }
      return new Promise((resolve, reject) => {
        axios(data)
          .then((data) => {
            resolve(data);
          })
          .catch((error) => {
            // 有重试次数
            if (typeof (data.try_count) == "number" && data.try_count > 0) {
              console.error("重试请求", error.message, data);
              data.try_count--;
              return setTimeout(() => {
                resolve(this.r_axios(data));
              }, data.try_time * 1000);
              // console.error('异常错误', error)
            } else {
              resolve(error);
            }
          });
      });
    },
    light_test() {},
    smart_list() {},
    //case更改
    CaseChange() {
      if (this.CaseData.col == "" || this.CaseData.col == null) {
        this.testData.col = "";
        this.CaseData.col = "";
      }
    },
    //本地保存视频选项设置
    save_video_state() {
      localStorage.setItem("video_state", this.video_state);
    },

    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    setCurrent(row) {
      this.$refs.singleTable.setCurrentRow(row);
    },
    //根据所选项赋值右侧case数据
    handleCurrentChange(val) {
      this.current_case = val;
      // console.log(val)
      // this.currentRow = val;

      this.testData.col = val["col"];
    },
    //删除测试项
    handleDelete(index, row) {
      if (this.CaseData.col.length == 1) {
        this.CaseData.col = [];
        this.testData.col = [];
      } else {
        this.CaseData.col.splice(index, 1);
      }
    },
    //删除case
    CaseDelete(index, row) {
      this.testData.col.splice(index, 1);
    },
    //增加case
    handleAdd() {
      let row = {
        CaseName: "",
        count: 30,
        result: "",
        isSet: true,
        col: [],
      };
      this.CaseData.col.push(row);
      this.CaseData.sel = row;
      console.log(this.CaseData);
    },
    //编辑状态判断
    handleChange(row, index, qx) {
      console.log("edit", this.CaseData, row.isSet);
      //点击修改，判断是否已经保存所有操作
      for (let i of this.CaseData.col) {
        console.log("i.isSet", i.isSet, i.CaseName, row.CaseName);
        if (i.isSet && i.CaseName != row.CaseName) {
          return false;
        }
      }
      //是否是取消操作
      if (!qx) {
        console.log("qx", this.CaseData.sel.CaseName);
        if (row.isSet) {
          this.CaseData.col.splice(index, 1);
        }
        return (row.isSet = !row.isSet);
      }
      //提交数据
      if (row.isSet) {
        console.log("this.CaseData.sel", this.CaseData.sel);
        const v = this.CaseData.sel;
        // 必填项判断
        if (v.CaseName == "" || v.count == "") {
          this.$message.warning("请输入测试名称或测试数量");
        } else {
          this.$emit("confirm", this.CaseData.sel);
          row.isSet = false;
        }
      } else {
        this.CaseData.sel = row;
        row.isSet = true;
      }
    },
    //增加case列
    CaseAdd() {
      for (let i of this.testData.col) {
        if (i.isSet) {
          return this.Message(
            this.$t("basicData.device.propDlg.pleSave"),
            "warning"
          );
        }
      }
      let row = {
        testName: "",
        time: 10,
        result: "",
        warning: false,
        isSet: true,
        content: "",
        log: [],
      };
      this.testData.col.push(row);
      this.testData.sel = row;
    },
    //修改测试项目数据
    valChange(row, index, qx) {
      console.log("edit", this.testData);
      //点击修改，判断是否已经保存所有操作
      for (let i of this.testData.col) {
        console.log("i.isSet", i.isSet, i.testName, row.testName);
        if (i.isSet && i.id != row.id) {
          return false;
        }
      }
      //是否是取消操作
      if (!qx) {
        console.log("qx", this.testData.sel.testName);
        if (!this.testData.sel.isSet) {
          this.testData.col.splice(index, 1);
        }
        return (row.isSet = !row.isSet);
      }
      //提交数据
      if (row.isSet) {
        console.log("this.testData.sel", this.testData.sel);
        const v = this.testData.sel;
        // 必填项判断
        if (v.testName == "" || v.time == "") {
          console.log("请选择测试项");
        } else {
          this.$emit("confirm", this.testData.sel);
          row.isSet = false;
        }
      } else {
        this.testData.sel = row;
        row.isSet = true;
      }
    }, //

    //全部usb设备列表
    async normal_list() {
      // const _ts=this
      this.auto_list_disable = true;
      const array_info = await this.get_usb_info();
      // console.log(array_info,'a')
      console.log(array_info);
      setTimeout(async () => {
        this.deviceData = array_info;
        // console.log(result,'c')
        this.auto_list_disable = false;
      }, 5000);
    },
    //usb设备数据处理
    async compair_data(data1, data2) {
      return new Promise((res, rej) => {
        let id_arr = [];
        let diff_arr = [];
        for (let j = 0; j < data1.length; j++) {
          id_arr.push(data1[j]["InstanceID"]);
        }
        for (let i = 0; i < data2.length; i++) {
          if (id_arr.includes(data2[i]["InstanceID"]) != true) {
            id_arr.push(data2[i]["InstanceID"]);
            diff_arr.push(data2[i]);
          }
        }
        res(diff_arr);
      });
    },
    //usb设备数据比对
    async check_data(data1, data2) {
      return new Promise((res, rej) => {
        let id_arr = [];
        let diff_arr = [];
        for (let j = 0; j < data2.length; j++) {
          id_arr.push(data2[j]["InstanceID"]);
        }
        for (let i = 0; i < data1.length; i++) {
          if (id_arr.includes(data1[i]["InstanceID"]) != true) {
            // id_arr.push(data1[i]["InstanceID"])
            diff_arr.push(data1[i]);
          }
        }
        res(diff_arr);
      });
    },
    //自动选择usb设备处理
    async auto_list() {
      if (this.firstRun == false) {
        this.$message.warning("请先设置串口");
        return;
      }
      if (!this.clientIp) {
        this.$message.warning("请先连接客户端");
        return;
      }
      this.auto_list_disable = true;
      let array_info;
      let array_infod;
      const _ts = this;
      // await this.initSerialPort()
      let str = "MoveTypeC-=" + this.ca["TypeC_dis"];
      const res = await this.portWrite(str);
      console.log(res);
      // console.log(2)
      setTimeout(async () => {
        array_info = await this.get_usb_info();
      }, 15000);
      // console.log(array_info,'a')
      setTimeout(async () => {
        console.log("second get device");
        let str = "MoveTypeC+=" + this.ca["TypeC_dis"];
        const res = await this.portWrite(str);
        console.log(res);
        setTimeout(async () => {
          array_infod = await _ts.get_usb_info();
          // console.log(array_infod)
          console.log(array_infod.length, array_info.length);
          let result = await _ts.compair_data(array_info, array_infod);
          _ts.deviceData = result;
          // console.log(result,'c')
          _ts.auto_list_disable = false;
        }, 15000);
      }, 20000);
    },
    //获取usb设备信息
    async get_usb_info() {
      return new Promise((resolve, reject) => {
        console.log("running", this.clientIp);
        const array_info = [];
        this.r_axios({
          url: "http://" + this.clientIp + ":3000/new_device_info",
          method: "get",
          data: {
            //  "testName":测试标题,
          },
          headers: {
            "Content-type": "application/json",
          },
          try_count: 12,
          try_time: 10,
        })
          .then((res) => {
            if (res.status == 200) {
              // this.deviceData2=res.data
              // console.log(res.data)
              if (res.data.indexOf("Microsoft PnP") != -1) {
                const dataArray1 = res.data.split("\r\n\r\n");
                const dataArray2 = dataArray1.filter(Boolean);
                // console.log(dataArray.splice('\r\n',''))

                let device_num = dataArray2.length;
                // console.log(device_num)

                for (let i = 0; i < dataArray2.length; i++) {
                  let array_info2 = {};
                  const dataArray = dataArray2[i].split("\r\n");

                  for (let i = 0; i < dataArray.length; i++) {
                    if (
                      dataArray[i].indexOf("实例 ID") != -1 ||
                      dataArray[i].indexOf("Instance ID") != -1
                    ) {
                      const InstanceArray = dataArray[i].split(":");
                      array_info2["InstanceID"] = InstanceArray[1].trim();
                    }
                    if (
                      dataArray[i].indexOf("设备描述") != -1 ||
                      dataArray[i].indexOf("Device Description") != -1
                    ) {
                      const Device_DescriptionArray = dataArray[i].split(":");
                      array_info2["DeviceDescription"] =
                        Device_DescriptionArray[1].trim();
                    }
                    if (
                      dataArray[i].indexOf("类名") != -1 ||
                      dataArray[i].indexOf("Class Name") != -1
                    ) {
                      const Class_NameArray = dataArray[i].split(":");
                      array_info2["ClassName"] = Class_NameArray[1].trim();
                    }
                    if (
                      dataArray[i].indexOf("类 GUID") != -1 ||
                      dataArray[i].indexOf("Class GUID") != -1
                    ) {
                      const Class_GUIDArray = dataArray[i].split(":");
                      array_info2["ClassGUID"] = Class_GUIDArray[1].trim();
                    }
                    if (
                      dataArray[i].indexOf("制造商名称") != -1 ||
                      dataArray[i].indexOf("Manufacturer Name") != -1
                    ) {
                      const Manufacturer_NameArray = dataArray[i].split(":");
                      array_info2["ManufacturerName"] =
                        Manufacturer_NameArray[1].trim();
                    }
                    if (
                      dataArray[i].indexOf("状态") != -1 ||
                      dataArray[i].indexOf("Status") != -1
                    ) {
                      const StatusArray = dataArray[i].split(":");
                      array_info2["Status"] = StatusArray[1].trim();
                    }
                    if (
                      dataArray[i].indexOf("驱动程序名称") != -1 ||
                      dataArray[i].indexOf("Driver Name") != -1
                    ) {
                      const Driver_NameArray = dataArray[i].split(":");
                      array_info2["DriverName"] = Driver_NameArray[1].trim();
                      array_info.push(array_info2);
                    }
                  }
                }
                resolve(array_info);
              }
            }
          })
          .catch((err) => {
            reject(err);
          });
      });
    },
    //光感数据获取以及格式化处理
    async light_detact(light_output_path, monitor_output_path) {
      const _this = this;
      console.log('start light detact')
      return (
        new Promise((resolve, reject) => {
          // console.log('start')
          // setTimeout(() =>{
                console.log('start read file')
                window.fs.readFile(
                  light_output_path,
                  "utf-8",
                  function (err, data) {
                    if (err) {
                      console.error(err);
                    } else {
                      // console.log("stdout: " + data);
                      if (data.indexOf("No Light Sensor Test kit found.") != -1) {
                        // this.error_msg="No Light Sensor Test kit found."
                        reject("No Light Sensor Test kit found.");
                      } else if (data.indexOf("Sensors Value") != -1) {
                        const dataArray = data.split("\r\n");
                        for (let i = 0; i < dataArray.length; i++) {
                          if (dataArray[i].indexOf("Sensors Value") != -1) {
                            const lightArray = dataArray[i];
                            console.log(lightArray);
                            resolve(lightArray);
                          }
                        }
                      }
                    }
                  }
                )
              // },1000);
        })
          // console.log('end')

          .then(async (array) => {
            console.log('start read data')
            let arrlight = array.split(" ").slice(3);
            let arrlight2 = [];
            for (let i = 0; i < arrlight.length; i++) {
              if (parseInt(arrlight[i].trim(), 16) >= 50) {
                arrlight2.push(true);
              } else {
                arrlight2.push(false);
              }
            }
            _this.lightSensorArray = arrlight2;
            console.log('start get monitor info')
            await _this.get_monitorInfo(monitor_output_path)
          })
      )
    },

    async get_monitorInfo(monitor_output_path){
      const _this=this
      await _this.r_axios({
              url: "http://" + _this.clientIp + ":3000/monitorInfo",
              method: "get",
              data: {
                //  "testName":测试标题,
              },
              headers: {
                "Content-type": "application/json",
              },
              try_count: 12,
              try_time: 10,
            })
              .then((res) => {
                if(res.data.indexOf('main')==-1){
                  return _this.get_monitorInfo(monitor_output_path)
                }
                else if (res.status == 200) {
                  // background-color: #4fc08d;
                  // this.fs.writeFile(monitor_output_path,res)
                  console.log('start write monitor infomation')
                  window.fs.writeFile(
                    monitor_output_path,
                    res.data,
                    (err, res) => {
                      if (err) return console.error(err);
                    }
                  );
                  const array = [];
                  if (res.data.includes("Monitor Info command line")) {
                    const dataArray1 = res.data.split("\r\n");
                    const dataArray = dataArray1.filter(Boolean);
                    let monitor_num;
                    let array_info2 = {};
                    for (let i = 0; i < dataArray.length; i++) {
                      if (dataArray[i].includes("How many Monitors")) {
                        const NumsArray = dataArray[i].split(":");
                        const num0 = NumsArray[0];
                        const num1 = NumsArray[1];
                        array_info2[num0] = num1;
                        console.log(NumsArray, array_info2);
                        monitor_num = parseInt(NumsArray[1]) + 1;
                      }
                      if (dataArray[i].includes("Monitor mode")) {
                        const ModeArray = dataArray[i].split(":");
                        array_info2[ModeArray[0]] = ModeArray[1];
                      }
                    }
                    // console.log(monitor_num);
                    _this.DisplayModeData = array_info2["Monitor mode"];
                    _this.DisplayNumData = array_info2["How many Monitors"];
                    for (let j = 1; j < monitor_num; j++) {
                      const array_info = {};
                      for (let i = 0; i < dataArray.length; i++) {
                        const now = "(" + (j + 1) + ")";
                        if (dataArray[i].includes(now) == true) {
                          break;
                        }
                        if (dataArray[i].includes("Monitor Module")) {
                          const monitorArray = dataArray[i].split(":");
                          array_info[monitorArray[0]] = monitorArray[1];
                        }
                        if (dataArray[i].includes("Resolution: ")) {
                          const resolutionArray = dataArray[i].split(":");
                          array_info[resolutionArray[0]] = resolutionArray[1];
                        }
                        if (dataArray[i].includes("Monitor Name")) {
                          const NameArray = dataArray[i].split(":");
                          array_info[NameArray[0]] = NameArray[1];
                        }
                      }
                      if (array_info != {}) {
                        console.log('数据不为空')
                        array.push(array_info);
                      }
                    }
                  }
                  _this.Display = array;
                  _this.$message.success("检测完成");
                } else {
                  _this.$message.warning("ERROR,格式不匹配", 1);
                }
              })
              .catch((res) => {
                _this.$message.warning("ERROR: " + res);
              })
          
          .catch((err) => {
            _this.$message.warning("ERROR: " + err);
          })
    },
    //调试函数
    async monitor_test() {
      // DisplayModeData:'',//显示模式
      // DisplayNumData:'',//显示器数量
      // Display:{},//显示器数据
      // console.log(JSON.parse(localStorage.getItem('DisplayModeData')),JSON.parse(localStorage.getItem('DisplayNumData')),JSON.parse(localStorage.getItem('lighSensorArray')),JSON.parse(localStorage.getItem('Display')))
      let st = JSON.parse(localStorage.getItem("lightSensorArray"));
      let st2 = JSON.parse(localStorage.getItem("Display"));
      let st3 = JSON.parse(localStorage.getItem("DisplayModeData"));
      let st4 = JSON.parse(localStorage.getItem("DisplayNumData"));
      await this.monitor();
      // await this.monitor_detact();
      console.log(this.lightSensorArray.toString() == st.toString());
      console.log(this.Display.sort().toString() == st2.sort().toString());
      console.log(this.DisplayModeData.toString() == st3.toString());
      console.log(this.DisplayNumData.toString() == st4.toString());
      // setTimeout(async () => {
      //   for (let i = 0; i < this.lightSensorArray.length; i++) {
      //     console.log(st[i] == this.lightSensorArray[i]);
      //   }
      // }, 3000);
    },
    //光感刷新
    async do_light_detact() {
      while (this.monitor_dialog) {
        setTimeout(async () => {
          await this.light_detact();
        }, 5000);
      }
    },
    async monitor(){
      let light_output_path = Vue.path.join(
        Vue.static_path,
        "./monitor_output.txt"
      );
      let monitor_output_path = Vue.path.join(
        Vue.static_path,
        "./monitorInfo.txt"
      );
      await this.monitor_detact()
      await this.light_detact(light_output_path, monitor_output_path)
    },
    //显示器检测
    monitor_detact() {
      let bat_path = Vue.path.join(Vue.static_path, "./run_monitor.bat");
      
      const _this = this;
      // console.log(light_output_path)
      const bat = new Promise((resolve, reject) => {
        const chd = Vue.child_process.execFile("cmd.exe", ["/c", bat_path]);
        chd.stdout.on("data", (data) => {
          console.log(data.toString());
          resolve("true");
        });
        chd.stderr.on("data", (data) => {
          console.error(data.toString());
          reject("false");
        });
        chd.on("exit", (code) => {
          console.log(`Child exited with code ${code}`);
        });
      });
      bat.then(async() => {
        console.log("bat running");
        // await _this.light_detact(light_output_path, monitor_output_path);
      });
      bat.catch((err) => {
        _this.$message.warning("ERROR," + err);
      });
    },
    //判断是否为可选项
    selectable(row, index) {
      if (row.status == 1) {
        return false;
      } else {
        return true;
      }
    },
    //列表已选项处理
    handleSelectionChange(val) {
      this.selectionData = val;
      for (let i = 0; i < this.selectionData.length; i++) {
        console.log(this.selectionData[i]);
      }
    },
    //监测log数据函数
    log_change(curVal, oldVal) {
      //串口数据处理
      //  let result=curVal.substring(oldVal.length,curVal.length)
      if (this.pullData.indexOf("BLK") != -1) {
        console.log("TypeC错误，请注意");
      } else {
        console.log("写入成功");
      }
    },
    //监测log变化函数
    log_set_height() {
      setTimeout(function () {
        let pullDataElement = document.getElementById("log_data");
        pullDataElement.scrollTop = pullDataElement.scrollHeight;
      }, 400);
    },
    //读取电机位置数据源函数
    readSerialPortData(type) {
      const str = "Read" + type;
      return new Promise((res, rej) => {
        try {
          this.portWrite(str);
        } catch (e) {
          rej(e);
        }
        setTimeout(() => {
          if (this.pullData == "") {
            rej("SerialPort Timeout");
          }
          res(parseInt(this.pullData.toString().split("=")[1]));
        }, 500);
      });
    },
    //读取三个电机位置数据
    async readSerialPort() {
      // // await this.portWrite("ReadTop")
      // // const Top_dis=this.portResult.toString().split('=')[1]
      // // this.ca['Top_dis']=Top_dis

      // await this.portWrite("ReadSide")
      // const Side_dis=this.portResult.toString().split('=')[1]
      // this.ca['Side_dis']=Side_dis

      // // await this.portWrite("ReadTypeC")
      // // const TypeC_dis=this.portResult.toString().split('=')[1]
      // // this.ca['TypeC_dis']=TypeC_dis
      // this.readSerialPortData('Top').then((data)=>{
      //     console.log(data)
      // })
      const _ts = this;
      await this.readSerialPortData("Side").then((data) => {
        _ts.ca["Side_dis"] = data;
        if (data != 0) {
          _ts.cd["Side"] = true;
        }
        console.log("Side=" + data);
      });
      await this.readSerialPortData("Top").then((data) => {
        _ts.ca["Top_dis"] = data;
        if (data != 0) {
          _ts.cd["Top"] = true;
        }
        console.log("Top=" + data);
      });
      await this.readSerialPortData("TypeC").then((data) => {
        _ts.ca["TypeC_dis"] = data;
        if (data != 0) {
          _ts.cd["TypeC"] = true;
        }
        console.log("TypeC=" + data);
      });

      // setTimeout(()=>{console.log(this.pullData)},400)
      // setTimeout(()=>{console.log(this.pullData)},5000)
    },
    //操作电机
    connect(type) {
      const port = this.port;
      if (port == "") {
        this.openCom();
      }
      if (this.cd[type] != true) {
        let num_dis2 = type + "_dis";
        let str = "Move" + type + "+=" + this.ca[num_dis2];
        console.log(str);
        const res = this.portWrite(str);
        if (res) {
          this.cd[type] = true;
        }
      } else {
        let num_dis2 = type + "_dis";
        let str = "Move" + type + "-=" + this.ca[num_dis2];
        // this.portwrite(str)
        console.log(str);
        const res = this.portWrite(str);
        if (res) {
          this.cd[type] = false;
        }
      }
    },
    //运行前确认函数
    start_confrim() {
      if (this.isSelectItem == true || this.running_id!=0) {
        this.start_fun();
        this.booking_dialog = false;
      }
      console.log(this.selectionData.length);
      if (this.selectionData.length > 1) {
        this.$message.warning("请只选择一项已预约项目");
        return;
      } else {
        this.running_id=this.selectionData[0].id
        this.start_fun();
      }
      this.booking_dialog = false;
    },
    //更新机器状态
    async update_server(machine, state) {
      await this.r_axios({
        url: "http://" + getBaseURL() + ":5000" + "/update_state",
        method: "post",
        data: {
          // "server": 测试机器,
          // "state": 机器状态,
          server: machine,
          state: state,
        },
        headers: {
          "Content-type": "application/json",
        },
        try_count: 12,
        try_time: 10,
      }).then((res) => {
        if (res.data.statuscode == 200) {
          return "true";
        } else {
          return "false";
        }
      });
    },
    //获取当前用户预约中的预约列表
    async get_user_book() {
      await this.r_axios({
        url: "http://" + getBaseURL() + ":5000" + "/get_user_book",
        method: "post",
        data: { user: getUser() },
        headers: {
          "Content-type": "application/json",
        },
        try_count: 12,
        try_time: 10,
      }).then((res) => {
        this.bookingdata = res.data;
      });
    },
    //获取下位机截图
    async get_screenshot(screenshot_path) {
      console.log(screenshot_path);
      let tempLink;
      let blobURL;

      const response = await this.r_axios({
        url: "http://" + this.clientIp + ":3000/screen_shot",
        method: "get",
        data: {
          //  "testName":测试标题,
        },
        headers: {
          "Content-type": "application/json",
        },
        try_count: 12,
        try_time: 10,
        responseType: "blob",
      })
        .then((res) => {
          const writer = Vue.fs.createWriteStream(screenshot_path);
          const ws = new WritableStream(writer);
          let blob = new Blob([res.data], { type: "image/jpg" });
          const stream = blob.stream();
          stream.pipeTo(ws);
        })
        .catch((rej) => {
          return "Get Screenshot Error(network)";
        });
      // response.data.pipe(writer)
      // return new Promise((resolve, reject) => {
      //   writer.on('finish', resolve)
      //   writer.on('error', reject)
      // })
    },
    //暂停测试
    async stop_test() {
      this.standing=true
      this.update_booking_content(this.running_id, "stop");
      this.run_state = false;
      this.START = true;
      this.first_Run=false
    },
    //更新预约内容，时间和测试机器
    async update_booking_content(book_id, state, first_Run) {
      console.log(book_id);

      // const id =book_id
      const time = new Date(Date.parse(new Date()));
      const _this = this;
      let bookingTime;
      let changeTime;
      if (first_Run) {
        bookingTime = time.toString();
        changeTime = time.toString();
        axios({
          url: "http://" + getBaseURL() + ":5000/update_booking_content",
          method: "post",
          data: {
            // "id":id,
            // "bookingTime": 执行日期,
            // "serverName":运行的机器,
            // "bookingState": 运行状态,
            id: book_id,
            serverName: this.ruleForm2.machine,
            bookingState: state,
            bookingTime: bookingTime,
            changeTime: changeTime,
          },
          headers: {
            "Content-type": "application/json",
          },
        }).then((res) => {
          if (res.data.statuscode == 200) {
            _this.update_server(this.ruleForm2.machine, "running");

            _this.showDialog2 = false;
            // 提交更新预约请求后提交更新服务器状态请求
          } else {
            this.pullData += "修改预约内容失败";
          }
        });
      } else {
        changeTime = time.toString();
        axios({
          url: "http://" + getBaseURL() + ":5000/update_booking_content",
          method: "post",
          data: {
            // "id":id,
            // "bookingTime": 执行日期,
            // "serverName":运行的机器,
            // "bookingState": 运行状态,
            id: book_id,
            serverName: this.ruleForm2.machine,
            bookingState: state,
            changeTime: changeTime,
          },
          headers: {
            "Content-type": "application/json",
          },
        }).then((res) => {
          if (res.data.statuscode == 200) {
            _this.update_server(this.ruleForm2.machine, "running");

            _this.showDialog2 = false;
            // 提交更新预约请求后提交更新服务器状态请求
          } else {
            this.pullData += "修改预约内容失败";
          }
        });
      }
    },
    //长时间无响应重启设备
    force_restart() {
      if (this.hasForce == true) {
        if (this.force_restart_state == 1) {
          this.portWrite("MoveTop+=" + this.ca["Top"]);
          setTimeout(async () => {
            await this.portWrite("MoveTop-=" + this.ca["Top"]);
          }, 10000);
        } else if (this.force_restart_state == 2) {
          this.portWrite("MoveSide+=" + this.ca["Side"]);
          setTimeout(async () => {
            await this.portWrite("MoveSide-=" + this.ca["Top"]);
          }, 10000);
        } else {
          return;
        }
      } else {
        return;
      }
    },
    //显示器检测
    async monitor_check(){
      this.log_data += "Start Monitor Check\n";
      const newP = new Promise((res, rej) => {
        setTimeout(async () => {
          console.log('start')
          await this.monitor_detact();
          console.log('finish')
          let error_msg = "";
          const oldDisplay = JSON.parse(
            localStorage.getItem("Display")
          );
          
          if (
            this.Display.sort().toString() !=
            oldDisplay.sort().toString()
          ) {
            error_msg += "Display Fail\n";
            console.log('Display Fail')
            var displayarr = [];
            for (let i = 0; i < this.Display.length; i++) {
              for (var nb in this.Display[i]) {
                if (nb == "Monitor Name") {
                  displayarr.push(this.Display[i][nb]);
                }
              }
            }
            for (let i = 0; i < oldDisplay.length; i++) {
              for (var na in oldDisplay[i]) {
                if (na == "Monitor Name") {
                  if (displayarr.indexOf(oldDisplay[i][na]) == -1) {
                    error_msg +=
                      oldDisplay[i][na].toString() + "Loss\n";
                      console.log(oldDisplay[i][na].toString() + "Loss\n")
                  }
                }
              }
            }
          }
          // let st=JSON.parse(localStorage.getItem('lightSensorArray'))
          // let st2=JSON.parse(localStorage.getItem('Display'))
          // let st3=JSON.parse(localStorage.getItem('DisplayModeData'))
          // let st4=JSON.parse(localStorage.getItem('DisplayNumData'))
          // console.log('check DisplayMode')
          if (
            this.DisplayModeData.toString() !=
            JSON.parse(localStorage.getItem("DisplayModeData"))
          ) {
            error_msg += "DisplayMode Fail\n";
            console.log('DisplayMode Fail')
          }

          if (
            this.DisplayNumData.toString() !=
            JSON.parse(localStorage.getItem("DisplayNumData"))
          ) {
            error_msg += "DisplayNum Fail\n";
            console.log('DisplayNum Fail')
          }
          const localLight = JSON.parse(
            localStorage.getItem("lightSensorArray")
          );
          
          if (
            (localLight[0] != this.lightSensorArray[0]) &
            (localLight[1] != this.lightSensorArray[1]) &
            (localLight[1] == true) &
            (localLight[0] == true)
          ) {
            error_msg += "Monitor1 Fail\n";
          }
          if (
            (localLight[2] != this.lightSensorArray[2]) &
            (localLight[3] != this.lightSensorArray[3]) &
            (localLight[3] == true) &
            (localLight[2] == true)
          ) {
            error_msg += "Monitor2 Fail\n";
          } 
          if (
            (localLight[4] != this.lightSensorArray[4]) &
            (localLight[5] != this.lightSensorArray[5]) &
            (localLight[5] == true) &
            (localLight[4] == true)
          ) {
            error_msg += "Monitor3 Fail\n";
          }
          if (
            (localLight[6] != this.lightSensorArray[6]) &
            (localLight[7] != this.lightSensorArray[7]) &
            (localLight[7] == true) &
            (localLight[6] == true)
          ) {
            error_msg += "Monitor4 Fail\n";
          }
          if (error_msg != "") {
              res(error_msg);
              console.log(error_msg)
             
          } 
          else {
            res("Pass")
            
          }
        }, 3000);
      });
      newP.then((res) => {
        if (res == "Pass") {
          this.log_data += "Monitor Check Pass\n";
          this.$message.success('pass')
          // this.CaseData.col[i].col[j].result = "Pass";
        } else {
          // let screenshot_path = Vue.path.resolve(
          //   log_path,
          //   "./" +
          //     this.CaseData.col[i].col[j].testName +
          //     "_" +
          //     v +
          //     ".jpg"
          // );
          // this.get_screenshot(screenshot_path);
          // this.upload_log(screenshot_path,3)
          this.log_data += "Monitor Check Fail\n" + res;
          this.$message.error('Check Fail'+res)
          // this.CaseData.col[i].col[j].result = "Fail";
          // this.hasFail = true;
          // this.CaseData.col[i].col[j].content = res;
        }
        console.log(this.log_data)
      });
      newP.catch((rej) => {});
    },
    //失败停止函数
    error_stop() {
      if (this.error_stop_state == 1) {
        if (this.hasFail == true) {
          this.isRunning = false;
        } else {
          return;
        }
      } else {
        return;
      }
    },
    //开始函数
    async start_fun() {
      let start_time = performance.now();
      if (this.CaseData.col.length < 1) {
        this.$message.warning("请选择测试项");
        return;
      }
      
      try{
        const _ts = this;
      this.hasFail = false;
      setMachine(this.ruleForm2.machine);
      this.START = false;
      this.isRunning = true;
      //更新预约状态
      await this.update_booking_content(this.running_id, "running");
      this.isSelectItem = true; //赋值已选预约项
      for (let i = 0; i < this.CaseData.col.length; i++) {
        if (this.isRunning != true) {
          this.stopLive();
          break;
        }

        //定义log路径和初始化运行数量等
        const log_path = await this.mk_log_dir(this.CaseData.col[i].CaseName);
        this.success_num = 0;
        this.fail_num = 0;
        this.run_num = 0;
        //根据用例文件的count字段设置循环执行的次数
        let have_to_run=0
        if (this.first_Run){
          have_to_run=this.CaseData.col[i].count
        }
        else{
          have_to_run=this.CaseData.col[i].count - this.run_num
        }
        for (let v = 0; v < have_to_run; v++) {
          if (this.isRunning != true) {
            this.stopLive();
            break;
          }
          for (let w = 0; w < this.CaseData.col[i].col.length; w++) {
            //重置运行结果
            this.CaseData.col[i].col[w].result = "";
            this.CaseData.col[i].col[w].content="";
          }
          this.tableData = this.CaseData.col[i].col;
          this.hasFail = false;
          this.hasForce = false;
          // const log_path=Vue.path.resolve(this.globlaStatic,"./Log/")
          const video_path = Vue.path.resolve(log_path, "../temp.mp4");
          const video_path2 = Vue.path.resolve(
            log_path,
            "./" + this.CaseData.col[i].CaseName + "_" + v + ".mp4"
          );
          if (this.video_state != 2) {
            this.openCamera(video_path);
          }
          this.log_data = "";
          let run_state='';
          this.all_num = this.CaseData.col[i].count;
          this.run_num = v;
          //根据用例文件的具体执行步骤的长度来循环执行每一步操作
          for (let j = 0; j < this.CaseData.col[i].col.length; j++) {
            if (this.isRunning == true) {
              let newP;
              //switch语句用于根据不同的操作名称执行不同操作
              switch (this.CaseData.col[i].col[j].testName) {
                case "plug in":
                  this.log_data += "Start Plug In\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(async () => {
                      if (this.cd["TypeC"] != true) {
                        let cmd = "MoveTypeC+=" + this.ca["TypeC_dis"];
                        try {
                          await this.portWrite(cmd);
                        } catch (e) {
                          rej("Fail,串口写入错误\n");
                        }
                        setTimeout(() => {
                          console.log(this.pullData);
                          if (this.pullData.indexOf("TypeC") == -1) {
                            rej("Fail,串口接入失败\n");
                          } else {
                            this.cd["TypeC"] = true;
                            res("Pass");
                          }
                        }, 2000);
                      } else {
                        res("Pass");
                      }
                    }, this.CaseData.col[i].col[j].time * 1000);
                  });
                  await newP.then((res) => {
                    if (res == "Pass") {
                      this.log_data += "Plug In Success\n";
                      this.CaseData.col[i].col[j].result = "Pass";
                    } else {
                      this.log_data += "Plug In Fail\n";
                      this.CaseData.col[i].col[j].result = "Fail";
                      this.CaseData.col[i].col[j].content = res;
                      this.isRunning = false;
                    }
                  });
                  await newP.catch((rej) => {});
                  break;

                case "plug out":
                  this.log_data += "Start Plug Out\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(async () => {
                      if (this.cd["TypeC"] == true) {
                        let cmd = "MoveTypeC-=" + this.ca["TypeC_dis"];
                        try {
                          await this.portWrite(cmd);
                          console.log(this.pullData);
                        } catch (e) {
                          this.log_data += "Plug Out Fail\n";
                          rej("Fail,串口拔出错误\n");
                        }
                        setTimeout(() => {
                          console.log(this.pullData);
                          if (this.pullData.indexOf("TypeC") == -1) {
                            this.log_data += "Plug Out Fail\n";
                            res("Fail,串口拔出失败\n");
                          } else {
                            this.cd["TypeC"] = false;
                            res("Pass");
                          }
                        }, 2000);
                      } else {
                        res("Pass");
                      }
                    }, this.CaseData.col[i].col[j].time * 1000);
                  });
                  await newP.then((res) => {
                    if (res == "Pass") {
                      this.log_data += "Plug Out Success\n";
                      this.CaseData.col[i].col[j].result = "Pass";
                    } else {
                      this.log_data += "Plug Out Fail\n";
                      this.CaseData.col[i].col[j].result = "Fail";
                      this.CaseData.col[i].col[j].content = res;
                    }
                  });
                  await newP.catch((rej) => {});
                  break;

                case "top button":
                  this.log_data += "Start Press Top Button\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(async () => {
                      if (this.cd["Top"] != true) {
                        let cmd = "MoveTop+=" + this.ca["Top_dis"];
                        try {
                          await this.portWrite(cmd);
                          cmd = "MoveTop-=" + this.ca["Top_dis"];
                          setTimeout(async () => {
                            await this.portWrite(cmd);
                          }, this.powerButton * 3000);
                        } catch (e) {
                          rej("Fail");
                        }
                        setTimeout(() => {
                          if (this.pullData.indexOf("Top") == -1) {
                            this.log_data += "Press Top Button Fail\n";
                            res("Fail,顶部按钮点击失败\n");
                          } else {
                            res("Pass");
                          }
                        }, 2000);
                      } else {
                        res("Pass");
                      }
                    }, this.CaseData.col[i].col[j].time * 3000);
                  });
                  await newP.then((res) => {
                    if (res == "Pass") {
                      this.log_data += "Press Top Button Success\n";
                      this.CaseData.col[i].col[j].result = "Pass";
                    } else {
                      this.log_data += "Press Top Button Fail\n";
                      this.CaseData.col[i].col[j].result = "Fail";
                      this.hasFail = true;
                      this.CaseData.col[i].col[j].content = res;
                    }
                  });
                  await newP.catch((rej) => {});
                  break;

                case "side button":
                  this.log_data += "Start Press Side Button\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(async () => {
                      let cmd = "MoveSide+=" + this.ca["Side_dis"];
                      try {
                        await this.portWrite(cmd);
                        cmd = "MoveSide-=" + this.ca["Side_dis"];
                        setTimeout(async () => {
                          await this.portWrite(cmd);
                        }, this.powerButton * 3000);
                      } catch (e) {
                        rej("Fail");
                      }
                      setTimeout(() => {
                        if (this.pullData.indexOf("Side") == -1) {
                          this.log_data += "Press Side Button Fail\n";
                          rej("Fail,右侧按钮点击失败/n");
                        } else {
                          res("Pass");
                        }
                      }, 1000);
                    }, this.CaseData.col[i].col[j].time * 3000);
                  });
                  await newP.then(() => {
                    this.log_data += "Press Side Button Success\n";
                    this.CaseData.col[i].col[j].result = "Pass";
                  });
                  await newP.catch((rej) => {
                    this.log_data += "Press SIde Button Fail\n";
                    this.CaseData.col[i].col[j].result = "Fail";
                    this.hasFail = true;
                    this.CaseData.col[i].col[j].content = rej;
                  });
                  break;

                case "device check":
                  this.log_data += "Start Device Check\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(async () => {
                      const data1 = JSON.parse(
                        localStorage.getItem("select_device_Data")
                      );
                      const data2 = await this.get_usb_info();
                      try {
                        console.log(data2.length, data1.length);
                        const re = await this.compair_data(data2, data1);
                        setTimeout(() => {
                          if (re.length >= 1) {
                            this.CaseData.col[i].col[j].result = "Fail";
                            let re_array=[]
                            for (let y=0;y<re.length;y++){
                              let re_a={}
                              re_a['DeviceDescription']=re[y]['DeviceDescription']
                              // re_a['ManufacturerName']=result[y]['ManufacturerName']
                              re_array.push(re_a)
                            }
                            this.log_data += JSON.stringify(re_array).toString()+" Loss\n";
                            res(JSON.stringify(re_array).toString() + "Loss\n");
                          } else {
                            res("Pass");
                          }
                        }, 1000);
                      } catch (e) {
                        this.hasForce = true;
                      }
                    }, this.CaseData.col[i].col[j].time * 1000);
                  });
                  await newP.then((res) => {
                    if (res == "Pass") {
                      this.log_data += "Device Check Pass\n";
                      this.CaseData.col[i].col[j].result = "Pass";
                    } else {
                      let screenshot_path = Vue.path.resolve(
                        log_path,
                        "./" +
                          this.CaseData.col[i].col[j].testName +
                          "_" +
                          v +
                          ".jpg"
                      );
                      this.get_screenshot(screenshot_path);
                      this.upload_log(screenshot_path,3)
                      this.log_data += "Device Check Fail\n";
                      this.CaseData.col[i].col[j].result = "Fail";
                      this.hasFail = true;
                      this.CaseData.col[i].col[j].content = res;
                    }
                  });
                  await newP.catch((rej) => {});
                  break;

                case 'monitor check':
                                        this.log_data+='Start Monitor Check\n'
                                        newP = new Promise((res,rej)=>{
                                        setTimeout(async()=>{
                                        await this.monitor()
                                        let error_msg=''
                                        const oldDisplay=JSON.parse(localStorage.getItem('Display'))
                                        
                                        if (this.Display.sort().toString()!=oldDisplay.sort().toString()){
                                            error_msg+=('Display Fail\n')
                                            var displayarr=[]
                                            for (let  i =0;i<this.Display.length;i++){
                                            for (var nb in this.Display[i]){
                                                if (nb=='Monitor Name'){
                                                    displayarr.push(this.Display[i][nb])
                                                }
                                            }
                                            }
                                            for (let  i =0;i<oldDisplay.length;i++){
                                            for (var na in oldDisplay[i]){
                                                if (na=='Monitor Name'){
                                                if(displayarr.indexOf(oldDisplay[i][na])==-1){
                                                    error_msg+=(oldDisplay[i][na].toString()+'Loss\n')
                                                }
                                                }
                                            }
                                            }
                                            
                                        }
                                        // let st=JSON.parse(localStorage.getItem('lightSensorArray'))
                                        // let st2=JSON.parse(localStorage.getItem('Display'))
                                        // let st3=JSON.parse(localStorage.getItem('DisplayModeData'))
                                        // let st4=JSON.parse(localStorage.getItem('DisplayNumData'))
                                        if (this.DisplayModeData.toString()!=JSON.parse(localStorage.getItem('DisplayModeData'))) {
                                            error_msg+=('DisplayMode Fail\n ')
                                        }
                                        if (this.DisplayNumData.toString()!=JSON.parse(localStorage.getItem('DisplayNumData'))){
                                            error_msg+=('DisplayNum Fail\n ')
                                        }
                                        const localLight=JSON.parse(localStorage.getItem('lightSensorArray'))
                                        if (localLight[0]!=this.lightSensorArray[0] & localLight[1]!=this.lightSensorArray[1] & localLight[1]==true & localLight==true){
                                            error_msg+=('Monitor1 Fail\n')
                                        }
                                        else if (localLight[2]!=this.lightSensorArray[2]&localLight[3]!=this.lightSensorArray[3] & localLight==true & localLight==true){
                                            error_msg+=('Monitor2 Fail\n')
                                        }
                                        else if (localLight[4]!=this.lightSensorArray[4]&localLight[5]!=this.lightSensorArray[5] & localLight==true & localLight==true){
                                            error_msg+=('Monitor3 Fail\n')
                                        }
                                        else if (localLight[6]!=this.lightSensorArray[6]&localLight[7]!=this.lightSensorArray[7] & localLight==true & localLight==true){
                                            error_msg+=('Monitor4 Fail\n')
                                        }
                                        if (error_msg!=''){
                                            res(error_msg)
                                        }    
                                        else(
                                            res('Pass')  
                                        )     
                                        },this.CaseData.col[i].col[j].time*1000)
                                        })
                                        await newP.then((res)=>{
                                            if(res=='Pass'){
                                                this.log_data+='Monitor Check Pass\n'
                                                this.CaseData.col[i].col[j].result='Pass'
                                            }
                                            else{
                                                let screenshot_path = Vue.path.resolve(
                                                  log_path,
                                                  "./" +
                                                    this.CaseData.col[i].col[j].testName +
                                                    "_" +
                                                    v +
                                                    ".jpg"
                                                );
                                                this.get_screenshot(screenshot_path);
                                                this.log_data+='Monitor Check Fail\n'+res+'\n'
                                                this.CaseData.col[i].col[j].result='Fail'
                                                this.CaseData.col[i].col[j].content=res
                                            }
                                            
                                        })
                                        await newP.catch((rej)=>{
                                            
                                            
                                        })
                                        break;
                                        
                case "power check":
                  this.log_data += "Start Power Check\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(async () => {
                      let charging = false;
                      await this.r_axios({
                        url: "http://" + this.clientIp + ":3000/get_battery",
                        method: "get",
                        data: {
                          //  "testName":测试标题,
                        },
                        headers: {
                          "Content-type": "application/json",
                        },
                        try_count: 12,
                        try_time: 10,
                      })
                        .then((re) => {
                          if (re.data.isCharging) {
                            charging = true;
                          } else {
                            charging = false;
                          }
                        })
                        .catch((error) => {
                          rej(error);
                        });
                      // console.log(charging)
                      if (charging == true) {
                        res("Pass");
                      } else {
                        res("UnCharging");
                      }
                    }, this.CaseData.col[i].col[j].time * 1000);
                  });
                  await newP.then((res) => {
                    if (res == "Pass") {
                      this.log_data += "Power Check Pass\n";
                      this.CaseData.col[i].col[j].result = "Pass";
                    } else {
                      let screenshot_path = Vue.path.resolve(
                        log_path,
                        "./" +
                          this.CaseData.col[i].col[j].testName +
                          "_" +
                          v +
                          ".jpg"
                      );
                      this.get_screenshot(screenshot_path);
                      this.upload_log(screenshot_path,3)
                      this.log_data += "Power Check Fail\n";
                      this.CaseData.col[i].col[j].result = "Fail";
                      this.hasFail = true;
                      this.CaseData.col[i].col[j].content = res;
                    }
                  });
                  await newP.catch(() => {});
                  break;

                case "s3":
                  this.log_data += "Start Device Action S3\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(() => {
                      this.r_axios({
                        url:
                          "http://" + this.clientIp + ":3000/device_action_s3",
                        method: "get",
                        data: {
                          //  "testName":测试标题,
                        },
                        headers: {
                          "Content-type": "application/json",
                        },
                        try_count: 12,
                        try_time: 10,
                      })
                        .then((re) => {
                          console.log(re.data);
                          if (re.data != "S3 ok") {
                            res("Fail");
                          } else {
                            res("Pass");
                          }
                        })
                        .catch(() => {});
                    }, this.CaseData.col[i].col[j].time * 1000);
                  });
                  await newP.then((res) => {
                    if (res == "Pass") {
                      this.log_data += "Device Action S3 Pass\n";
                      this.CaseData.col[i].col[j].result = "Pass";
                    } else {
                      this.log_data += "Device Action S3 Fail\n";
                      this.CaseData.col[i].col[j].result = "Fail";
                      this.hasFail = true;
                      this.CaseData.col[i].col[j].content = res;
                    }
                  });
                  await newP.catch((rej) => {});
                  break;

                case "s4":
                  this.log_data += "Start Device Action S4\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(() => {
                      this.r_axios({
                        url:
                          "http://" + this.clientIp + ":3000/device_action_s4",
                        method: "get",
                        data: {
                          //  "testName":测试标题,
                        },
                        headers: {
                          "Content-type": "application/json",
                        },
                        try_count: 12,
                        try_time: 10,
                      })
                        .then((re) => {
                          if (re.data != "S4 ok") {
                            res("Fail");
                          } else {
                            res("Pass");
                          }
                        })
                        .catch((err) => {
                          rej(err);
                        });
                    }, this.CaseData.col[i].col[j].time * 1000);
                  });
                  await newP.then((res) => {
                    if (res == "Pass") {
                      this.log_data += "Device Action S4 Pass\n";
                      this.CaseData.col[i].col[j].result = "Pass";
                    } else {
                      this.log_data += "Device Action S4 Fail\n";
                      this.CaseData.col[i].col[j].result = "Fail";
                      this.hasFail = true;
                      this.CaseData.col[i].col[j].content = res;
                    }
                  });
                  await newP.catch((rej) => {});
                  break;

                case "s5":
                  this.log_data += "Start Device Action S5\n";
                  newP = new Promise((res, rej) => {
                    setTimeout(() => {
                      this.r_axios({
                        url:
                          "http://" + this.clientIp + ":3000/device_action_s5",
                        method: "get",
                        data: {
                          //  "testName":测试标题,
                        },
                        headers: {
                          "Content-type": "application/json",
                        },
                        try_count: 12,
                        try_time: 10,
                      })
                      .then((re) => {
                        // console.log(re.data)
                        // console.log(re.data.data)
                        if (re.data != "S5 ok") {
                          res("Fail");
                        } else {
                          res("Pass");
                        }
                      })
                      .catch((err) => {
                          rej(err);
                        });
                    }, this.CaseData.col[i].col[j].time * 1000)
                  });

                  await newP.then((res) => {
                    console.log(res)
                    if (res == "Pass") {
                      this.log_data += "Device Action S5 Pass\n";
                      this.CaseData.col[i].col[j].result = "Pass";
                    } else {
                      this.log_data += "Device Action S5 Fail\n";
                      this.CaseData.col[i].col[j].result = "Fail";
                      this.hasFail = true;
                      this.CaseData.col[i].col[j].content = res;
                    }
                  });
                  await newP.catch((rej) => {});
                  break;
              }
            }
            //测试运行时手动中断测试
            else {
              this.log_data += "测试中断";
              this.stopLive();
              this.update_booking_content(this.running_id, "stop");
              break;
            }
          }
          //根据是否有result 为非Pass判断本次执行是否pass
          for (let w = 0; w < this.CaseData.col[i].col.length; w++) {
            
            if (this.CaseData.col[i].col[w].result != "Pass") {
              run_state = "Fail";
              break;
            } else {
              run_state = "Pass";
            }
          }
          //向log字段添加带运行结果的数据
          this.CaseData.col[i].log.push(this.CaseData.col[i].col);
          this.run_num += 1;
          if (run_state != "Pass") {
            this.fail_num += 1;
          } else {
            this.success_num += 1;
          }
          //根据设置的录制视频的状态以及运行结果来决定是否保存视频文件以及后续的转码操作
          if (this.video_state != 2) {
            await this.stopLive();
          }
          if (run_state == "Pass") {
            if (this.video_state == 3) {
              console.log("保存全部视频");
              setTimeout(async () => {
                //视频转码
                await this.video_encode(video_path, video_path2);
                await this.upload_log(video_path2, 3);

              }, 2000);
            } 
            else {
              console.log("不保存视频");
            }
          } 
          else {
            if (this.video_state == 1) {
              console.log("保存失败视频");
               //获取失败截图和视频并上传文件数据
                let screenshot_path = Vue.path.resolve(
                  log_path,
                  "./" + this.CaseData.col[i].CaseName + "_" + v + ".jpg"
                );
                await this.get_screenshot(screenshot_path);
                await this.video_encode(video_path, video_path2);
                await this.upload_log(video_path2, 3);
                await this.upload_log(screenshot_path, 3);
              
            }
          }
          //删除转码前不可用于直接播放的文件
          // Vue.fs.unlink(video_path, (err) => {
          //   if (err) {
          //     throw err;
          //   }
          //   console.log("文件：" + video_path + "删除成功");
          // });
          //生成log文件并上传文件数据
          this.pullData += "开始生成LOG文件";
          await this.mk_log_file(i, log_path);

          this.pullData += "开始上传Log文件";
          const filename = Vue.path.resolve(
            log_path,
            this.CaseData.col[i].CaseName + ".xls"
          );
          await this.upload_log(filename, 3);
        }
      }
      }
      catch(e){
        this.$message.error(e)
      }
      //更新预约项目的状态
      let end_time = performance.now();
      let rtime = end_time - start_time;
      const runtime = (rtime / 3600000).toFixed(2);
      await this.upload_run_time(runtime);
      await this.update_booking_content(this.running_id, "Finish");
    },
    //上传运行时间数据
    async upload_run_time(run_time) {
      axios({
        url: "http://" + getBaseURL() + ":5000/upload_run_time",
        method: "post",
        headers: {
          "Content-type": "application/json",
        },
        data: {
          machine: this.ruleForm2.machine,
          run_time: run_time,
        },
      })
        .then((res) => {
          if (res.data.statuscode == 200) {
            console.log("运行时间上传成功");
            this.pullData += "运行时间上传成功";
          } else {
            console.log("运行时间上传失败");
            this.pullData += "运行时间上传失败";
          }
        })
        .catch((rej) => {
          console.log("运行时间上传失败");
          this.pullData += "运行时间上传失败,错误原因：" + rej;
        });
    },
    downloadFile() {
      let file_path = "D:\\0105\\serve\\serve\\static\\Log\\1.mp4";
      var instance = axios.create();
      let filename = file_path.split("\\")[file_path.split("\\").length - 1];
      let type = filename.split(".")[1];
      let file_type;
      if ((type == "mp4") | (type == "MP4")) {
        file_type = "video/mp4";
      } else if (type == "xlsx") {
        file_type =
          "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
      } else if (type == "xls") {
        file_type = "application/vnd.ms-excel";
      } else if ((type == "jpg") | (type == "jpeg")) {
        file_type = "image/jpeg";
      } else if (type == "png") {
        file_type = "image/png";
      } else if (type == "zip") {
        file_type = "application/zip";
      } else {
        file_type = "application/json";
      }
      //  protocol: 'http:',
      // hostname: '127.0.0.1',//127.0.0.1:3001/get_log
      // port: 3001,
      // path: '/get_log',
      instance({
        url: "http://127.0.0.1:3001/get_log",
        method: "post",
        data: {
          filepath: file_path,
        },
        headers: {
          "Content-type": "application/json",
        },
        responseType: "blob",
      })
        .then((res) => {
          // console.log(res);
          const blob = new Blob([res.data], { type: file_type });
          if (typeof window.navigator.msSaveBlob !== "undefined") {
            // 兼容IE，window.navigator.msSaveBlob：以本地方式保存文件
            window.navigator.msSaveBlob(blob, decodeURI(filename));
          } else {
            // 创建新的URL并指向File对象或者Blob对象的地址
            const blobURL = window.URL.createObjectURL(blob);
            // 创建a标签，用于跳转至下载链接
            const tempLink = document.createElement("a");
            tempLink.style.display = "none";
            tempLink.href = blobURL;
            tempLink.setAttribute("download", decodeURI(filename));
            // 兼容：某些浏览器不支持HTML5的download属性
            if (typeof tempLink.download === "undefined") {
              tempLink.setAttribute("target", "_blank");
            }

            // 挂载a标签
            document.body.appendChild(tempLink);
            tempLink.click();
            document.body.removeChild(tempLink);
            // 释放blob URL地址
            window.URL.revokeObjectURL(blobURL);
          }
        })
        .catch((rej) => {
          // console.log(rej);
        });
    },
    //上传Log 数据
    async upload_log(file_path, retry_num) {
      let upload_state=true
      await axios({
          url: "http://" + getBaseURL() + ":5000/check_log_exsit",
          method: "post",
          headers: {
            "Content-type": "application/json",
          },
          data: {
            file_path: file_path,
          },
        })
          .then((res) => {
            if (res.data.statuscode == 400) {
               upload_state=false
            } 
          })
      if(upload_state){
        return
      }
      let size;
      let create_time;
      let type = file_path.split(".")[1];
      let typenum;
      if ((type == "mp4") | (type == "mov")) {
        typenum = 1;
      } else if (
        (type == "xls") |
        (type == "xlsx") |
        (type == "word") |
        (type == "ppt")
      ) {
        typenum = 2;
      } else if ((type == "png") | (type == "jpg") | (type == "jpeg")) {
        typenum = 3;
      } else {
        typenum = 0;
      }
      let filename = file_path.split("\\")[file_path.split("\\").length - 1];
      await Vue.fs.stat(file_path, (err, stats) => {
        if (err) {
          console.log(err);
          return false;
        } else {
          // // 检测类型，是文件还是目录
          // console.log(`文件：${stats.isFile()}`);
          // console.log(`目录：${stats.isDirectory()}`);
          if (stats.isFile()) {
            // 文件大小
            size = stats.size;
            console.log(`size:${stats.size}`);
            // 创建时间
            create_time = stats.birthtime;
            console.log(`birth time: ${stats.birthtime}`);
            // 最后一次修改时间
            console.log(`modified time:${stats.mtime}`);
          }
        }
      });
      setTimeout(() => {
        axios({
          url: "http://" + getBaseURL() + ":5000/upload_log",
          method: "post",
          headers: {
            "Content-type": "application/json",
          },
          data: {
            filename: filename,
            file_path: file_path,
            machine: this.ruleForm2.machine,
            type: typenum.toString(),
            create_user: getUserName(),
            size: size.toString(),
            create_time: create_time,
          },
        })
          .then((res) => {
            console.log(res);
            if (res.data.statuscode == 200) {
              console.log("文件上传成功");
              this.pullData += "文件上传成功";
            } else {
              console.log("文件上传失败");
              this.pullData += "文件上传失败";
            }
          })
          .catch((rej) => {
            if (retry_num != 0) {
              console.log("文件上传失败,重试中" + rej.toString());
              this.pullData += "文件上传失败,重试中" + rej.toString();
              retry_num--;
              return this.upload_log(file_path, retry_num);
            } else {
              console.log("文件上传失败超过三次,请联系管理员" + rej.toString());
              this.pullData +=
                "文件上传失败超过三次,请联系管理员" + rej.toString();
            }
          });
      }, 2000);
    },
    //更新服务器ip
    async update_server_ip() {
      this.r_axios({
        url: "http://110.40.182.32:5000/get_ip",
        method: "get",
        headers: {
          "Content-type": "application/json",
        },
      }).then((res) => {
        localStorage.setItem("server_ip", res.data.ip);
      });
    },
    //创建log文件
    async mk_log_file(i, log_path) {
      const e4 = Vue.e4;
      var wb = new e4.Workbook(); // 创建实例
      let data = this.CaseData.col;
      console.log(data[i].CaseName);

      var ws = wb.addWorksheet(data[i].CaseName); // 页名
      var style = wb.createStyle({
        // 样式（可自行查看官方API）
        font: {
          // 标题使用
          color: "#50878a",
          size: 12,
          bold: true,
        },
        alignment: {
          wrapText: true,
          horizontal: "center",
          vertical: "center",
        },
        fillColor: "#ABABAB",
      });
      var style2 = wb.createStyle({
        // 样式二
        font: {
          size: 12,
          color: "red",
        },
        alignment: {
          wrapText: true,
          horizontal: "center",
          vertical: "center",
        },
      });
      var line = 2;
      ws.cell(1, 1).string("testStep").style(style);
      ws.cell(1, 2).string("testStep").style(style); // 不是从下标开始的，第一行第一列
      ws.cell(1, 3).string("time").style(style); // 不是从下标开始的，第一行第二列
      ws.cell(1, 4).string("result").style(style); // 不是从下标开始的，第一行第三列
      ws.cell(1, 5).string("content").style(style);
      ws.cell(1, 6).string("PowerCheck").style(style);
      ws.cell(1, 7).string("DeviceCheck").style(style);
      ws.cell(1, 8).string("MonitorCheck").style(style);

      ws.column(1).setWidth(50); // 第1列的宽度
      ws.column(2).setWidth(50); // 第2列的宽度
      ws.column(3).setWidth(50); // 第3列的宽度
      ws.column(4).setWidth(50); // 第4列的宽度
      ws.column(5).setWidth(50); // 第5列的宽度
      ws.column(6).setWidth(50); // 第6列的宽度
      ws.column(7).setWidth(50); // 第7列的宽度
      ws.column(8).setWidth(50); // 第8列的宽度

      const log_length = data[i].log.length;
      for (var k = 0; k < log_length; k++) {
        var testStep = "";
        var errorContent = "";
        const logk_length = data[i].log[k].length;
        for (var j = 0; j < logk_length; j++) {
          ws.row(j + 1).setHeight(20); // 第2行以及之后的高度
          errorContent += JSON.stringify(data[i].log[k][j].content);
          testStep +=
            ">> " +
            JSON.stringify(data[i].log[k][j].testName)
              .replace('"', "")
              .replace('"', "");
          switch (data[i].log[k][j].testName) {
            case "monitor check": {
              if (data[i].log[k][j].result == "Pass") {
                ws.cell(line, 8)
                  .string(JSON.stringify(data[i].log[k][j].result))
                  .style(style);
              } else {
                ws.cell(line, 8)
                  .string(JSON.stringify(data[i].log[k][j].result))
                  .style(style2);
              }
              break;
            }
            case "power check": {
              if (data[i].log[k][j].result == "Pass") {
                ws.cell(line, 6)
                  .string(JSON.stringify(data[i].log[k][j].result))
                  .style(style);
              } else {
                ws.cell(line, 6)
                  .string(JSON.stringify(data[i].log[k][j].result))
                  .style(style2);
              }
              break;
            }
            case "device check": {
              if (data[i].log[k][j].result == "Pass") {
                ws.cell(line, 7)
                  .string(JSON.stringify(data[i].log[k][j].result))
                  .style(style);
              } else {
                ws.cell(line, 7)
                  .string(JSON.stringify(data[i].log[k][j].result))
                  .style(style2);
              }
              break;
            }
          }
          ws.cell(line, 3)
            .string(JSON.stringify(new Date(Date.parse(new Date()))))
            .style(style);
          ws.cell(line, 4).string(JSON.stringify(data[i].result)).style(style);
          ws.cell(line, 5).string(JSON.stringify(errorContent)).style(style);
        }
        ws.cell(line, 1).string(line-1).style(style);
        ws.cell(line, 2).string(testStep).style(style);
        line += 1;
      }
      const filename = Vue.path.resolve(
        log_path,
        this.CaseData.col[i].CaseName + ".xls"
      );
      wb.write(filename);
    },
    //时间转换
    async date_trans(timestrip) {
      const time_a = ["s", "m", "h"];
      const time_b = [1000, 60000, 3600000];
      let str;
      for (let i = 0; i < 3; i++) {
        if (timestrip / time_b[i] > 1) {
          str = timestrip / time_b[i] + time_a[i];
        } else {
          break;
        }
      }
      return str;
    },
    //创建LOG目录
    async mk_log_dir(CaseName) {
      const date = new Date();
      const day = date.getDay();
      const month = date.getMonth() + 1;
      const years = date.getFullYear();
      const hours = date.getHours();
      const min = date.getMinutes();
      const sec = date.getSeconds();
      let log_path = Vue.path.resolve(this.globlaStatic, "./Log/");

      await window.fs.mkdir(log_path, { recursive: true }, (err) => {
        if (err) {
          throw err;
        } else {
          console.log("Created Folder!");
        }
      });

      const filedir = Vue.path.resolve(
        log_path,
        CaseName +
          years +
          "-" +
          month +
          "-" +
          day +
          "--" +
          hours +
          "-" +
          min +
          "-" +
          sec
      );
      // console.log(filedir)
      await window.fs.mkdir(filedir, { recursive: true }, (err) => {
        if (err) {
          throw err;
        } else {
          console.log("Created Folder!");
        }
      });
      return filedir;
    },
    //初始化串口
    initSerialPort() {
      console.log("init");
      // this.closeCom();
      if (!this.firstRun) {
        let _ts = this;
        // if (_ts.port){
        //     return
        // }
        for (let i in _ts.ports) {
          _ts.com = _ts.ports[i].label;
          console.log(_ts.com);
          _ts.openCom();
          const res = _ts.readSerialPort();
          res.then(() => {
            _ts.$message.success("Open SerailPort Success");
            _ts.SerialPortAvaliable = true;
          });
          res.catch(console.log("Error,try other serialport"));
          if (_ts.SerialPortAvailable) {
            break;
          }
        }
        if (_ts.SerialPortAvaliable == false) {
          _ts.$message.warning("Error,serialport unavailable");
        }
        this.reSetSerialport();
        this.firstRun = true;
      }
      //
      // return
      // this.readSerialPort()
    },
    //取消停止
    cancelEvent(){
      console.log('cancel stop!')
    },
    //停止测试
    stop_fun() {
      //停止按钮
      this.START = true;
      this.isRunning = false;
      this.stopLive();
      this.update_booking_content(this.running_id, "finish")
    },
    //重置串口位置
    async reSetSerialport() {
      await this.portWrite("ReadTop");
      const top_dis = parseInt(this.pullData.toString().split("=")[1]);
      await this.portWrite("MoveTop-=" + top_dis);

      await this.portWrite("ReadSide");
      const Side_dis = parseInt(this.pullData.toString().split("=")[1]);
      await this.portWrite("MoveSide-=" + Side_dis);

      await this.portWrite("ReadTypeC");
      const TypeC_dis = parseInt(this.pullData.toString().split("=")[1]);
      await this.portWrite("MoveTypeC-=" + TypeC_dis);

      console.log(top_dis, TypeC_dis, Side_dis);
    },
    async add_dis(type) {
      //串口距离增加
      const port = this.port;
      if (port == "") {
        this.openCom();
      }
      let num_dis2 = type + "_dis";
      let num = type + "_num";
      // this.ca[num_dis2] += this.ca[num];
      let str = "Move" + type + "+=" + this.ca[num];
      // this.portwrite(str)
      console.log(str);
      const res = this.portWrite(str);
      if (res) {
        this.cd[type] = true;
      }
      setTimeout(() => {
        if (this.pullData.indexOf(type) == -1) {
          this.$message.error("增加出错，请注意");
        }
      }, 500);

      await this.readSerialPortData(type).then((data) => {
        if (this.ca[num_dis2] != data) {
          this.ca[num_dis2] = data;
          console.log(this.ca[num_dis2]);
        }
      });
    },
    async cut_dis(type) {
      //串口数据减少
      const port = this.port;
      if (port == "") {
        this.openCom();
      }
      let num_dis2 = type + "_dis";
      let num = type + "_num";
      if (this.ca[num_dis2] - this.ca[num] < 0) {
        this.ca[num_dis2] = 0;
      } else {
        // this.ca[num_dis2] -= this.ca[num];
        let str = "Move" + type + "-=" + this.ca[num];
        // this.portwrite(str)
        console.log(str);
        const res = this.portWrite(str);
        if (res) {
          this.cd[type] = true;
        }
        if (this.pullData.indexOf(type) == -1) {
          this.$message.error("减少出错，请注意");
        }
        await this.readSerialPortData(type).then((data) => {
          if (this.ca[num_dis2] != data) {
            this.ca[num_dis2] = data;
            console.log(this.ca[num_dis2]);
          }
        });
      }
    },
    showopenDialog() {
      this.dialogVisible = true;
    },
    save_to_file(fileName) {
      //保存为文件
      if (this.CaseData.sel == null) {
        this.$message.warning("请设置测试用例后保存");
        return;
      }
      var elementA = document.createElement("a");
      //文件的名称加文件后缀
      elementA.download = fileName + ".json";
      elementA.style.display = "none";
      // this.CaseData.col['log']=[]
      //生成一个blob二进制数据，内容为json数据
      var blob = new Blob([JSON.stringify(this.CaseData.col)]);

      //生成一个指向blob的URL地址，并赋值给a标签的href属性
      elementA.href = URL.createObjectURL(blob);
      document.body.appendChild(elementA);
      elementA.click();
      document.body.removeChild(elementA);
    },
    save_data(data_type) {
      //保存数据
      // const store=new Vue.Storer()
      if (data_type == "serialPort") {
        try {
          localStorage.setItem("serial_port_Data", JSON.stringify(this.ca));
          localStorage.setItem("serial_port_state", JSON.stringify(this.cd));
        } catch (e) {
          this.$message.warning(e);
          return "error";
        }
        this.$message.success("保存成功");
      } else if (data_type == "usb") {
        if (this.selectionData == "") {
          this.$message.warning("已选列表为空，请选择设备");
          return "error";
        }
        try {
          localStorage.setItem(
            "select_device_Data",
            JSON.stringify(this.selectionData)
          );
        } catch (e) {
          this.$message.warning(e);
          return "error";
        }

        this.$message.success("保存成功");
      } else if (data_type == "monitor") {
        // Display
        // lightSensorArray
        // DisplayModeData
        // DisplayNumData
        try {
          localStorage.setItem("Display", JSON.stringify(this.Display));
          localStorage.setItem(
            "lightSensorArray",
            JSON.stringify(this.lightSensorArray)
          );
          localStorage.setItem(
            "DisplayModeData",
            JSON.stringify(this.DisplayModeData)
          );
          localStorage.setItem(
            "DisplayNumData",
            JSON.stringify(this.DisplayNumData)
          );
        } catch (e) {
          this.$message.warning(e);
          return "error";
        }

        this.$message.success("保存成功");
      } else if (data_type == "testCase") {
        // Display
        // lightSensorArray
        // DisplayModeData
        // DisplayNumData
        try {
          localStorage.setItem("testCase", JSON.stringify(this.CaseData.col));
          for (let i = 0; i < this.CaseData.col.length; i++) {
            let arr = [];
            arr["testName"] = this.CaseData.col[i].CaseName;
            // this.tableData='';
            this.tableData.push(arr);
          }
        } catch (e) {
          this.$message.warning(e);
          return "error";
        }

        this.$message.success("保存成功");
      } else {
        this.$message.warning("ERROR");
        return "error";
      }
    },
    cmdTe(cmdStr) {
      //执行命令行命令
      const cmdpath = "./";
      const decoder = new window.TextDecoder("gbk");
      const cmd = Vue.child_process.spawn(cmdStr, { cwd: cmdpath });
      cmd.stdout.on("data", (data) => {
        console.log("stdout:" + decoder.decode(data));
      });
      cmd.stderr.on("data", (data) => {
        console.log("stderr:" + decoder.decode(data));
      });
      cmd.on("data", (data) => {
        console.log("out code:" + decoder.decode(data));
      });
    },
    tableRowClassName({ row, rowIndex }) {
      //根据数据返回样式,给不同状态反馈
      if (row.result === "Pass") {
        return "success-row";
      } else if (row.result === "Fail") {
        return "warning-row";
      }
      return "";
    },
    sensorClassName(sensor) {
      //根据光感数据返回光感弹框的样式
      if (sensor == false) {
        return "white-row";
        // +parseInt(sensor, 10) >= 5
      } else if (sensor) {
        return "green-row";
      }
      return "red-row";
    },
    //开关用例集
    openCase() {
      this.showForcase = false;
    },
    closeCase() {
      this.showForcase = true;
    },
    //选择文件
    handleFileChange(e) {
      // 同样触发一个 input 来配合 v-model 的使用
      let file = this.$refs.file;
      file.click();
    },
    //获取ip
    getIp() {
      //获取ip
      const IPAddress = Vue.ip.address();

      let ip_arr = IPAddress.split(".");
      for (let i = 0; i < this.ipAddress.length; i++) {
        this.ipAddress[i].value = ip_arr[i];
      }
    },
    //错误提示函数
    error_show_dialog(error_msg){
      this.errorVisible=true
      this.errormsg=error_msg
    },
    //捕获错误函数
    async catchError(){
      try{
        await this.get_client_status()
      }
      catch(e){
        this.error_msg=e
      }
      
    },
    //获取客户端状态
    get_client_status() {
      let remote = window.electron.remote;
      const client_ip = remote.getGlobal("client_ip");
      // const cip = localStorage.getItem('ip')
      this.clientIp = client_ip;
      const _this = this;
      // console.log(this.clientIp+':clientIp')
      // try{
        if (this.clientIp) {
        this.r_axios({
          url: "http://" + this.clientIp + ":3000",
          method: "get",
          data: {
            //  "testName":测试标题,
          },
          headers: {
            "Content-type": "application/json",
          },
          try_count: 12,
          try_time: 20,
        })
          .then((res) => {
            if (res.status == 200) {
              // background-color: #4fc08d;
              document.getElementById("ip").style.backgroundColor = "#4fc08d";
            } else {
              document.getElementById("ip").style.backgroundColor = "#E81123";
            }
          })
          .catch((res) => {
            document.getElementById("ip").style.backgroundColor = "#E81123";
          });
      } else {
        _this.$message.warning("未有客户端ip接入");
        if (this.isRunning == true) {
          _this.$message.error("客户端断开连接");
        }
      }
      // }
      // catch(e){
      //   this.error_show_dialog(e)
      // }
      
    },
    //确认ip
    comfirm_ip() {
      // let options = {name: 'electron sudo application'};
      // let cmd='cd '+
      // Vue.sudo.exec('start ./static/Monitor.exe /monitor', options,
      // (error, stdout, stderr)=>{
      //   if (error) throw error;
      //   console.log('stdout: ' + stdout);
      // })
      let ip_arr = [];
      for (let i in this.ipAddress) {
        ip_arr.push(this.ipAddress[i].value);
      }
      let ip = ip_arr.join(".");
      this.checkStatus(ip);
    },
    //检查链接状态
    checkConnect() {
      if (this.clientIp != "") {
        this.r_axios({
          url: "http://" + this.clientIp + ":3000/",
          method: "get",
          data: {
            //  "testName":测试标题,
          },
          headers: {
            "Content-type": "application/json",
          },
          try_count: 12,
          try_time: 10,
        })
          .then((res) => {
            console.log(res);
            if (res.status == 200) {
              // background-color: #4fc08d;
              document.getElementById("ip").style.backgroundColor = "#4fc08d";
            } else {
              document.getElementById("ip").style.backgroundColor = "#E81123";
            }
          })
          .catch((res) => {
            document.getElementById("ip").style.backgroundColor = "#E81123";
          });
      }
    },
    //检查链接
    checkStatus(ip) {
      const _this = this;
      if (ip == "...") {
        _this.$message.warning("请输入ip地址");
      }
      this.r_axios({
        url: "http://" + ip + ":3000/",
        method: "get",
        data: {
          //  "testName":测试标题,
          ip: ip,
        },
        headers: {
          "Content-type": "application/json",
        },
        try_count: 12,
        try_time: 10,
      })
        .then((res) => {
          console.log(res);
          if (res.status == 200) {
            // background-color: #4fc08d;
            document.getElementById("ip").style.backgroundColor = "#4fc08d";
          } else {
            _this.$message.warning("网络错误，请检查ip地址或网络状况");
          }
        })
        .catch((res) => {
          _this.$message.warning("网络错误，请检查ip地址或网络状况");
        });
    },
    //选择用例文件
    fileChange(e) {
      // 同样触发一个 input 来配合 v-model 的使用
      var reader = new FileReader();
      let file = this.$refs.file.files[0];
      let filename = file.name;
      let index = filename.lastIndexOf(".");
      //获取后缀
      let ext = filename.substr(index + 1);
      if (ext != "json") {
        // this.$confirm('请选择文本文件')
        //     .then(_ => {
        //         console.log("pass")
        //     })
        //     .catch(_ => {});
        this.error_show = true;
      } else {
        this.error_show = false;
        reader.readAsText(file);
        reader.onload = (e) => {
          this.CaseData.col = ''
          this.CaseData.col = JSON.parse(e.target.result);
          this.testFileName = file.name;
        };
      }
      // setTimeout(this.error_show=false,3000)
    },
    //视频转码
    async video_encode(video_path, save_path) {
      const cmdpath = Vue.path.join(this.globlaStatic, "./ffmpeg/bin");
      //    const video_path=Vue.path.join(this.globlaStatic,"./videotemp/vedio_recording.mp4")
      const cmdStr =
        "ffmpeg.exe -y -i " + video_path + " " + save_path + " -qp 0";
      //    const decoder=new window.TextDecoder('gbk')
      const cmd = Vue.child_process.exec(cmdStr, { cwd: cmdpath });
      cmd.stdout.on("data", (data) => {
        console.log("stdout:" + data);
      });
      cmd.stderr.on("data", (data) => {
        console.log("stderr:" + data);
      });
      cmd.on("data", (data) => {
        console.log("out code:" + data);
      });
    },
    //下载视频
    downLoadVideo(chunks) {
      let downloadLink = document.createElement("a");
      let blob = new Blob(chunks, {
        type: "video/mp4",
      });
      downloadLink.href = URL.createObjectURL(blob);
      // downloadLink.download = 'live.webm';
      downloadLink.download = "live.MP4";
      // downloadLink.download = 'live.mp4';
      downloadLink.click();
      console.log("recorder stopped");
    },
    // 结束录制视频，触发基数录制视频事件
    stopLive() {
      // console.log(this.mediaRecorderData,"this.mediaRecorderData");
      if (this.record_state) {
        console.log(this.mediaRecorder);
        this.mediaRecorder.stop();
        console.log("停止录制");
      }
      this.closeCamera();
      this.record_state = false;
    },
    //保存视频文件
    async saveLive(chunks, path) {
      let blob = "";
      // let responsePromise = await fetch(url)
      blob = new Blob(chunks, {
        type: "video/mp4",
      });
      // let downloadLink = document.createElement("a");
      // downloadLink.href = URL.createObjectURL(blob);
      // downloadLink.download = "live.MP4";
      // downloadLink.click();
      // console.log("downloading");

      let reader = new FileReader();

      reader.onload = async () => {
        let buffer = new Buffer(reader.result);
        console.log("开始写入");
        const video_path = path;
        await window.fs.writeFileSync(video_path, buffer, (err, res) => {
          if (err) return console.error(err);
        });
      };
      reader.readAsArrayBuffer(blob);
      console.log(reader);
      reader.onerror = (err) => console.error(err);
      // const a = document.createElement('a')
      // a.style.display = 'none'
      // let url = URL.createObjectURL(blob)
      // a.href = url
      // a.download = Date.now() + 'fileName' + '.mp4'
      // a.click()
      // a.remove()
      // a.href = url
      // a.download = Date.now() + 'fileName' + '.mp4'
      // document.body.appendChild(a)
      // a.click()
      // setTimeout(() => {
      // URL.revokeObjectURL(url)
      // document.body.removeChild(a)
      // }, 100)
    },
    //开始录制视频
    startRecording(stream, path) {
      // console.log(stream,"stream");
      // this.logger();
      let Chunks = [];
      let l = console.log;
      this.mediaRecorder = new MediaRecorder(stream);
      this.mediaRecorder.start();
      this.mediaRecorder.addEventListener("dataavailable", function (e) {
        if (e.data.size > 0) Chunks.push(e.data);
      });

      this.mediaRecorder.addEventListener("stop", () => {
        console.log("暂停 自动下载");
        this.saveLive(Chunks, path);
      });

      this.mediaRecorder.addEventListener("start", (e) => {
        l("开始 录制");
        this.record_state = true;
      });
    },
    //关闭摄像头
    closeCamera() {
      if (this.mediaRecorder == null || this.record_state == false) {
        console.log("未开始录制");
      } else {
        this.thisVideo.srcObject.getTracks()[0].stop();
        console.log("关闭摄像头");
      }
    },
    //打开摄像头
    openCamera(path) {
      this.thisVideo = this.$refs.videoCamera;
      var constraints = {
        audio: false,
        video: {
          width: 1920,
          height: 1080,
        },
        frameRate: { ideal: 60, max: 120 },
      };
      let promise = navigator.mediaDevices.getUserMedia(constraints);
      promise.then((stream) => {
        let liveVideo = document.querySelector("video");

        liveVideo.srcObject = stream;
        liveVideo.play();
        liveVideo.addEventListener("play", () => {
          console.log("video start");
        });
        liveVideo.addEventListener("ended", () => {
          console.log("video end");
        });
        liveVideo.addEventListener("pause", () => {
          console.log("video pause");
        });
        liveVideo.addEventListener("stop", () => {
          console.log("video stop");
        });
        this.startRecording(stream, path);
      });
    },
    //表格拖动排序
    dragSort() {
      const el = this.$refs.singleTable.$el.querySelectorAll(
        ".el-table__body-wrapper > table > tbody"
      )[0];
      this.sortable = Sortable.create(el, {
        ghostClass: "sortable-ghost",
        setData: function (dataTransfer) {
          dataTransfer.setData("Text", "");
        },
        onEnd: (e) => {
          //e.oldIndex为拖动一行原来的位置，e.newIndex为拖动后新的位置
          const targetRow = this.tableData.splice(e.oldIndex, 1)[0];
          this.tableData.splice(e.newIndex, 0, targetRow);
          console.log(this.tableData, "排序后的数据");
          let dragId = this.tableData[e.newIndex].id; //拖动行的id
          let oneId, twoId;
          //拖动行的前一行
          if (this.tableData[e.newIndex - 1]) {
            oneId = this.tableData[e.newIndex - 1].id;
          } else {
            oneId = "";
          }
          //拖动行的后一行
          if (this.tableData[e.newIndex + 1]) {
            twoId = this.tableData[e.newIndex + 1].id;
          } else {
            twoId = "";
          }
          console.log(dragId, oneId, twoId);
          this.postRequest({
            url: this.dragUrl,
            data: {
              dragId: dragId,
              oneId: oneId,
              twoId: twoId,
              projectId: "",
            },
            success: (result) => {
              if (result) {
                this.$message({
                  message: "拖动排序成功!",
                  type: "success",
                });
              } else {
                this.$message({
                  message: "拖动排序失败！",
                  type: "error",
                });
              }
            },
          });
        },
      });
    },
    //判断ip输入是否为数字
    checkIpVal(item, index) {
      let self = this;
      //确保每个值都处于0-255
      let val = item.value;
      //当输入的是空格时，值赋为空
      val = val.trim();
      val = parseInt(val, 10);
      let e = event || window.event;

      if (isNaN(val)) {
        val = "";
      } else {
        val = val < 0 ? 0 : val;
        val = val > 255 ? 255 : val;
        if (val < 0) {
          val = "";
        } else if (val.toString().length > 2) {
          if (val < 255) {
            self.$refs.ipInput[index + 1].focus();
          } else {
            val = 255;
          }
          if (index > 2) {
            self.$refs.ipInput[3].focus();
          } else {
            self.$refs.ipInput[index + 1].focus();
          }
        }
      }
      item.value = val;
    },
    //判断ip是否满足三位数字，移到下一个输入框
    turnIpPOS(item, index, event) {
      let self = this;
      let e = event || window.event;
      // console.log(index);
      //删除键把当前数据删除完毕后会跳转到前一个input，左一不做任何处理
      if (e.keyCode == 8) {
        let val = item.value;
        console.log(val);
        if (index == 0) {
        } else if (val == "") {
          self.$refs.ipInput[index - 1].focus();
        }
      }
      //右箭头、回车键、空格键、冒号均向右跳转，右一不做任何措施
      if (
        e.keyCode == 39 ||
        e.keyCode == 13 ||
        e.keyCode == 32 ||
        e.keyCode == 190 ||
        e.keyCode == 110
      ) {
        if (index == 3) {
        } else {
          self.$refs.ipInput[index + 1].focus();
        }
      }
    },
    //当input失去焦点，而ip没有赋值时，会自动赋值为0
    setDefaultVal(item) {
      let val = item.value;
      if (val == "") {
        item.value = "";
      }
    },
    //开启串口
    openCom() {
      // if (window.portisOpen){
      //     trans_function.$emit('openCom')
      // }
      // else{
      //      this.$router.push({path:'/serialport/index'})
      // }
      let _ts = this;
      let port = _ts.port;
      if (port) {
        _ts.closeCom();
      }
      port = new Vue.SerialPort(_ts.com, _ts.options);
      port.open(function (err) {
        if (err) {
          _ts.$message.warning("err");
          // _ts.$set(_ts, "stateText", err);
          _ts.stateText = err;

          new Notification("LAAT", {
            body: err,
          });
          // _ts.$message.warning(err)
          return console.log("Error opening port: ", err);
        }
        _ts.$message.success("success");
        // Because there's no callback to write, write errors will be emitted on the port:
        // _ts.$set(_ts, "state", 1);
        _ts.state = 1;
        // _ts.$set(_ts, "stateText", "串口已开启");
        _ts.stateText = "串口已开启";
        // _ts.$set(_ts, "isOpen", true);
        _ts.isOpen = true;
        // _ts.$set(_ts, "port", port);
        _ts.port = port;
      });

      // The open event is always emitted
      port.on("open", async function () {
        // open logic
        console.log("打开中...");
        console.log("串口打开状态", port.isOpen);
        this.portIsOpen = true;
        window.portisOpen = port.isOpen;
      });

      port.on("error", (error) => {
        //捕获错误
        new Notification("LAAT", {
          body: error,
        });
        // _ts.$set(_ts, "stateText", error);
        _ts.stateText = error;
        console.log("Error: ", error);
        window.data_state = false;
      });
      // 串口设备传来的数据 是buffer对象，用toString转一下码
      port.on("data", function (data) {
        // 判断是否显示接收的数据
        let pullData;
        if (_ts.isGBK(data)) {
          let encoding = "GB2312";
          pullData = window.iconv.decode(data, encoding);
        } else {
          pullData = data.toString();
        }

        // _ts.$set(_ts, "pullData", _ts.pullData + pullData);
        _ts.pullData = _ts.pullData + pullData;
        setTimeout(function () {
          let pullDataElement = document.getElementById("pullDta");
          pullDataElement.scrollTop = pullDataElement.scrollHeight;
        }, 400);
        window.pullData = pullData;
        window.data_state = true;

        // _ts.pullData=pullData
      });
    },
    //串口写入
    portWrite(content) {
      let _ts = this;
      let port = _ts.port;
      _ts.pushData = content.toString();
      let encoding = "utf-8";
      _ts.pullData = "";
      let pushData;
      // if (port && port.isOpen && _ts.pushData) {
      //     let encoding = 'utf-8';
      //     let pushData;
      //     if (_ts.hexSend) {
      //         if (!_ts.sendState) {
      //             return;
      //         }
      //         encoding = 'hex';
      //         pushData = _ts.handlerHex(_ts.pushData);
      //     } else {
      pushData = window.iconv.encode(_ts.pushData, "GB2312");
      // }
      port.write(pushData, encoding, function (err, result) {
        if (err) {
          console.log("Error while sending message : " + err);
          window.data_state = false;
          window.result = err;
          return false;
        }
        if (result) {
          console.log("Response received after sending message : " + result);
          window.data_state = true;
          window.result = result;
        }
        // 接收字节数
        // _ts.$set(_ts, "pushBit", _ts.pushBit + _ts.pushData.length);
        _ts.pushBit = _ts.pushBit + _ts.pushData.length;
        console.log("message written");
      });
      port.drain((error) => {
        if (error) {
          console.log(error);
          window.data_state = false;
          return false;
        }
      });
      window.data_state = true;
      return true;
    },
    //hex转码
    handlerHex(data) {
      let _ts = this;
      let dataStr = "";
      if (data.trim().indexOf(" ") > -1) {
        let hexArr = data.trim().split(" ");
        for (let i in hexArr) {
          if (hexArr[i].length == 1) {
            dataStr += "0";
          }
          dataStr += hexArr[i];
        }
      } else {
        dataStr = _ts.handlerHexDisplay(data.trim());
      }
      return dataStr.replace(/\s+/g, "");
    },
    //字符串转二进制
    strToBinary(str, binary) {
      let result = [];
      let list = str.split("");
      for (let i = 0; i < list.length; i++) {
        let item = list[i];
        let data = item.charCodeAt(0).toString(binary);
        result.push(data);
      }
      return result.join("");
    },
    //判读是否gbk编码
    isGBK(data) {
      if (data[0] == 0xff && data[1] == 0xfe) {
        console.log("unicode");
        return false;
      } else if (data[0] == 0xfe && data[1] == 0xff) {
        console.log("unicode");
        return false;
      } else if (data[0] == 0xef && data[1] == 0xbb) {
        console.log("utf8");
        return false;
      } else {
        return true;
      }
    },
    //串口检测
    genPorts() {
      let _ts = this;
      Vue.SerialPort.list().then(
        (ports) => {
          let portList = new Array();
          for (let i in ports) {
            let port = {
              label: ports[i].path,
              value: ports[i].path,
            };
            portList.push(port);
          }
          if (portList.length > 0) {
            portList.sort(function (port1, port2) {
              let s1 = _ts.strToBinary(port1.value, 10);
              let s2 = _ts.strToBinary(port2.value, 10);
              return s1 - s2;
            });
            // _ts.$set(_ts, "stateText", "初始化成功");
            _ts.stateText = "初始化成功";
            if (!_ts.com) {
              // _ts.$set(_ts, "com", portList[0].value);
              _ts.com = portList[0].value;
            }
          } else {
            // _ts.$set(_ts, "stateText", "未获取到串口信息");
            _ts.stateText = "未获取到串口信息";
            // _ts.$set(_ts, "com", "");
            _ts.com = "";
          }
          // _ts.$set(_ts, "ports", portList);
          _ts.ports = portList;
          // _ts.initSerialPort();
          // for(let i in ports){
          //     _ts.com=portList[i].label
          //     _ts.openCom()
          //     this.portWrite("ReadTop")
          //     setTimeout(()=>{
          //         if(_ts.pullData==''){
          //             console.log(_ts.com+" is not available")

          //         }
          //         else{
          //             console.log("open "+_ts.com+"success")
          //             return
          //         }
          //     },400)
          // }
        },
        (err) => console.error(err)
      );
    },
    //渲染进程初始化数据
    genRenderer() {
      let _ts = this;
      // let ipcRenderer = window.electron.ipcRenderer;
      let remote = window.electron.remote;
      const SerialPort = remote.getGlobal("SerialPort");
      Vue.Readlines = SerialPort.parsers.Readline;
      this.globlaStatic = remote.getGlobal("__static");
      const usb = remote.getGlobal("usb");
      const static_path = remote.getGlobal("static_path");
      window.iconv = remote.getGlobal("iconv");
      window.fs = remote.getGlobal("fs");
      const console = remote.getGlobal("log");

      const child_process = remote.getGlobal("child_process");
      const path = remote.getGlobal("path");
      const readline = remote.getGlobal("readline");
      const diff = remote.getGlobal("diff");
      const diffString = remote.getGlobal("diffString");
      const e4 = remote.getGlobal("e4");
      Vue.e4 = Vue.prototype.$e4 = e4;
      Vue.diff = Vue.prototype.$diff = diff;
      Vue.diffString = Vue.prototype.$diffString = diffString;

      Vue.SerialPort = Vue.prototype.$SerialPort = SerialPort;

      Vue.usb = Vue.prototype.$usb = usb;
      Vue.fs = Vue.prototype.$fs = window.fs;
      Vue.static_path = Vue.prototype.$static_path = static_path;
      Vue.child_process = Vue.prototype.$child_process = child_process;
      const ip = remote.getGlobal("ip");
      Vue.ip = Vue.prototype.$ip = ip;
      Vue.path = Vue.prototype.$path = path;
      Vue.readline = Vue.prototype.$readline = readline;
      const client_ip = remote.getGlobal("client_ip");
      const cip = localStorage.getItem("ip");
      this.clientIp = cip != null ? cip : client_ip;
      // DisplayNumData
      // DisplayModeData
      // lightSensorArray
      // Display
      // selectionData
      // ca
      // localStorage.setItem('serial_port_Data',this.ca);
      // localStorage.setItem('serial_port_state',this.cd);
      this.DisplayNumData =
        localStorage.getItem("DisplayNumData") != null
          ? JSON.parse(localStorage.getItem("DisplayNumData"))
          : "";
      this.DisplayModeData =
        localStorage.getItem("DisplayModeData") != null
          ? JSON.parse(localStorage.getItem("DisplayModeData"))
          : "";
      this.lightSensorArray =
        localStorage.getItem("lightSensorArray") != null
          ? JSON.parse(localStorage.getItem("lightSensorArray"))
          : [false, false, false, false, false, false, false, false];
      this.Display =
        localStorage.getItem("Display") != null
          ? JSON.parse(localStorage.getItem("Display"))
          : {};
      this.video_state =
        localStorage.getItem("video_state") != null
          ? localStorage.getItem("video_state")
          : 3;
      this.selectionData =
        localStorage.getItem("selectionData") != null
          ? localStorage.getItem("selectionData")
          : [];
      this.ca =
        localStorage.getItem("serial_port_Data") != null
          ? JSON.parse(localStorage.getItem("serial_port_Data"))
          : {
              Top_dis: 0,
              Top_num: 100,
              Side_dis: 0,
              Side_num: 100,
              TypeC_dis: 0,
              TypeC_num: 100,
            };
      this.cd =
        localStorage.getItem("serial_port_state") != null
          ? JSON.parse(localStorage.getItem("serial_port_state"))
          : { Top: false, Side: false, TypeC: false };
      // this.DisplayNumData= localStorage.getItem('DisplayNumData')
      // this.DisplayModeData= localStorage.getItem('DisplayModeData')
      // this.lightSensorArray= localStorage.getItem('lightSensorArray')
      // this.Display=localStorage.getItem('Display')
      // this.selectionData= localStorage.getItem('selectionData')
      // this.ca= localStorage.getItem('ca')
      // this.cd= localStorage.getItem('cd')
      console.log(this.clientIp + "cip");
      _ts.genPorts();
    },
    //关闭串口
    closeCom() {
      let _ts = this;
      let port = _ts.port;
      if (port) {
        try {
          if (port.isOpen) {
            port.close();
            // window.portisOpen=port.isOpen
          } else {
            _ts.genPorts();
          }
          // _ts.$set(_ts, 'state', 0);
          // _ts.$set(_ts, 'isOpen', false);
          // _ts.$set(_ts, 'stateText', '串口已关闭');
          console.log("关闭中...");
          console.log("串口打开状态", port.isOpen);
          this.portIsOpen = false;
        } catch (e) {
          // _ts.$set(_ts, 'stateText', e);
          console.log(e);
        }
      }
    },
    //隐藏滚动条
    scrollbarShowHidden(element) {
      element.addClass("scrollbarHide");
      element.hover(
        function () {
          element.addClass("scrollbarShow");
        },
        function () {
          element.removeClass("scrollbarShow");
        }
      );
    },
  },
  mounted() {
    this.genRenderer();
    this.dragSort();
    this.getIp();
    window.portisOpen = true;
    this.timer = setInterval(this.catchError, 5000);
    this.timer2 = setInterval(set_server_ip(), 1800000);
    // this.$nextTick(()=>{
    //     this.initSerialPort();
    // })
  },
  created() {},
  beforeDestroy() {
    clearInterval(this.timer);
    clearInterval(this.timer2);
  },
  // },
  // beforeRouteEnter(to, form, next) {

  //     console.log(form)
  // },
  // beforeRouteLeave(to, form, next) {

  //     console.log(to)
  // }
};
</script>

<style scoped>
.main {
  overflow: visible;
}
.white-row {
  background: rgb(255, 255, 255);
  width: 70px;
  border-top: 1px dashed skyblue;
  border-right: 1px dashed skyblue;
  border-left: 1px dashed skyblue;
  border-bottom: 1px dashed skyblue;
  margin-left: 10px;
  display: inline-block;
  text-align: center;
  border-radius: 5px;
}
.green-row {
  background: #01b1ad;
  width: 70px;
  border-top: 1px dashed skyblue;
  border-right: 1px dashed skyblue;
  border-left: 1px dashed skyblue;
  border-bottom: 1px dashed skyblue;
  margin-left: 10px;
  display: inline-block;
  text-align: center;
}
.red-row {
  background: rgb(249, 222, 250);
  width: 70px;
  border-top: 1px dashed skyblue;
  border-right: 1px dashed skyblue;
  border-left: 1px dashed skyblue;
  border-bottom: 1px dashed skyblue;
  margin-left: 10px;
  display: inline-block;
  text-align: center;
}
.el-table .success-row {
  background: lightblue;
}
.el-table .warning-row {
  background: rgb(250, 250, 222);
}

.el-form .el-button {
  margin-top: 10px;
}
.el-btn {
  width: 90px;
  margin-left: 10px;
  text-align: center;
}
.el-btn2 {
  width: 90px;
  margin-left: 15px;
  text-align: center;
}
.el-btn3 {
  width: 120px;
  margin-left: 20px;
}
.el_input {
  margin: 10px 10px;
}
.frame_home {
  min-width: 380px;
}
.frame_home .frame {
  border-top: 1px dashed skyblue;
  border-right: 1px dashed skyblue;
  border-left: 1px dashed skyblue;
  border-bottom: 1px dashed skyblue;
  padding-top: 5px;
  padding-right: 5px;
  padding-left: 5px;
  padding-bottom: 5px;
  border-radius: 5px;
}
.frame_home .frame .el-col {
  margin-top: 10px;
}
*::-webkit-scrollbar {
  width: 7px;
  height: 10px;
  background-color: transparent;
} /*定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/
*::-webkit-scrollbar-track {
  background-color: #f0f6ff;
} /*定义滚动条轨道 内阴影+圆角*/
*::-webkit-scrollbar-thumb {
  background-color: skyblue;
  border-radius: 6px;
} /*定义滑块 内阴影+圆角*/
.scrollbarHide::-webkit-scrollbar {
  display: none;
}
.scrollbarShow::-webkit-scrollbar {
  display: block;
}

.video_area {
  min-width: 630px;
  min-height: 370px;
  margin-left: 20px;
  border-top: 1px dashed skyblue;
  border-right: 1px dashed skyblue;
  border-left: 1px dashed skyblue;
  border-bottom: 1px dashed skyblue;
  padding-top: 5px;
  padding-right: 5px;
  padding-left: 5px;
  padding-bottom: 5px;
  border-radius: 5px;
}
.log_area {
  min-width: 630px;
  min-height: 233px;
  border-top: 1px dashed skyblue;
  border-right: 1px dashed skyblue;
  border-left: 1px dashed skyblue;
  border-bottom: 1px dashed skyblue;
  padding-top: 5px;
  padding-right: 5px;
  padding-left: 5px;
  padding-bottom: 5px;
  border-radius: 5px;
  margin-left: 20px;
  margin-top: 20px;
}
.ipAdress {
  display: flex;
  align-items: center;
  border: 1px solid gainsboro;
  width: 300px;
  height: 50px;
  font-size: 30px;
  margin-top: 10px;
  padding: 10px;
  border-radius: 10px;
}

.ipAdress li {
  position: relative;
  height: 23px;
  margin: 0;
  list-style: none;
  margin-top: -10px;
}

ul[class="ipAdress"] input[type="text"] {
  border: none;
  width: 100%;
  text-align: center;
  background: transparent;
  color: #000;
}

.ipAdress li div {
  position: absolute;
  top: 5px;
  right: 0;
  padding: auto;
}

/*只需要3个div*/
.ipAdress li:last-child div {
  display: none;
}

/*取消掉默认的input focus状态*/
.ipAdress input:focus {
  outline: none;
}
.ipAddr {
  margin-left: 20px;
  display: inline-block;
}

#settingBtn {
  position: relative;
  z-index: 1;
  width: 280px;
  float: left;
  height: 215px;
}
#settingBtn .el-button {
  margin-top: 10px;
  width: 120px;
}
</style>
