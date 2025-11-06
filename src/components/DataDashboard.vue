<template>
  <div class="dashboard-container">
    <!-- 主标题区域 -->
    <div class="dashboard-title">
      <h1>冷热电联供综合能源系统数字孪生运维管控平台</h1>
    </div>
    
    <!-- 顶部区域：图片和控制按钮 -->
    <div class="top-section">
      <!-- 左侧控制区 -->
      <div class="left-controls">
        <!-- 实时日期时间（两行显示） -->
        <div class="datetime-display">
            <div class="date-line">{{ currentDate }}</div>
            <div class="time-line">{{ currentTime }}</div>
        </div>
        <!-- 左侧按钮组 -->
        <div class="left-buttons">
            <button class="dashboard-button primary" @click="handleOptimizationClick">
              <span class="button-icon">⚡</span>
              <span>运行优化</span>
            </button>
            <button class="dashboard-button primary" @click="handleDiagnosisClick">
              <span class="button-icon">🔍</span>
              <span>故障诊断</span>
            </button>
        </div>
      </div>
      
      <!-- 中央图片区域（占3/5） -->
      <div class="central-image-container">
        <div class="equipment-image-wrapper">
          <!-- 3D模型容器 -->
          <div ref="modelContainer" style="width: 100%; height: 100%; position: absolute;"></div>
          
          <!-- 设备看板弹窗 -->
          <div v-if="dashboardVisible && selectedDevice" class="device-dashboard-modal" @click.self="closeDeviceDashboard">
            <div class="device-dashboard">
              <div class="dashboard-header">
                <h3>{{ selectedDevice.name }}信息看板</h3>
                <button class="close-button" @click="closeDeviceDashboard">×</button>
              </div>
              <div class="dashboard-content">
                <!-- 燃气发电机看板 -->
                <div v-if="selectedDevice.id === 'generator'" class="dashboard-grid">
                  <!-- 当前数据 -->
                  <div class="dashboard-item">
                    <div class="item-label">发电Uab</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].generator.Uab }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">A相电流</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].generator.currentA }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">总有功功率</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].generator.powerTotal }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">总无功功率</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].generator.reactiveTotal }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">发电频率</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].generator.frequency }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">发电机转速</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].generator.speed }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">排气温度</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].generator.exhaustTemp }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">并网开关状态</div>
                    <div class="item-value measured" :class="systemData[currentSystemState].generator.gridSwitch === '合闸' ? 'normal' : 'abnormal'">
                      {{ systemData[currentSystemState].generator.gridSwitch }}
                    </div>
                  </div>
                  <!-- 数字孪生数据 -->
                  <div class="dashboard-item">
                    <div class="item-label">效率预测</div>
                    <div class="item-value digital-twin">95.2%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">故障概率</div>
                    <div class="item-value digital-twin">0.3%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">剩余寿命预测</div>
                    <div class="item-value digital-twin">8760h</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">检修倒计时</div>
                    <div class="item-value predicted">90天</div>
                  </div>
                </div>
                
                <!-- 溴化锂机组看板 -->
                <div v-if="selectedDevice.id === 'lithium'" class="dashboard-grid">
                  <!-- 当前数据 -->
                  <div class="dashboard-item">
                    <div class="item-label">冷水供水温度</div>
                    <div class="item-value">{{ systemData[currentSystemState].lithium.coldInTemp }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">冷水回水温度</div>
                    <div class="item-value">{{ systemData[currentSystemState].lithium.coldOutTemp }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">烟气进口温度</div>
                    <div class="item-value">{{ systemData[currentSystemState].lithium.smokeInTemp }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">烟气出口温度</div>
                    <div class="item-value">{{ systemData[currentSystemState].lithium.smokeOutTemp }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">冷却水供水温度</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].lithium.coolInTemp }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">冷却水回水温度</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].lithium.coolOutTemp }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">蒸发器温度</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].lithium.evaporatorTemp }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">蒸发器压力</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].lithium.evaporatorPress }}</div>
                  </div>
                  <!-- 数字孪生数据 -->
                  <div class="dashboard-item">
                    <div class="item-label">效率预测</div>
                    <div class="item-value digital-twin">86.5%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">故障概率</div>
                    <div class="item-value digital-twin">0.5%</div>
                  </div>

                  <div class="dashboard-item">
                    <div class="item-label">能耗预测</div>
                    <div class="item-value digital-twin">235kWh/日</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">检修倒计时</div>
                    <div class="item-value predicted">78天</div>
                  </div>
                </div>
                
                <!-- 电网系统看板 -->
                <div v-if="selectedDevice.id === 'powerGrid'" class="dashboard-grid">
                  <div class="dashboard-item">
                    <div class="item-label">市电Uab</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].powerGrid.Uab }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">市电Ubc</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].powerGrid.Ubc }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">市电Uca</div>
                    <div class="item-value measured">{{ systemData[currentSystemState].powerGrid.Uca }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">系统状态</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '运行中' : '已停机' }}
                    </div>
                  </div>
                  <!-- 数字孪生数据 -->
                  <div class="dashboard-item">
                    <div class="item-label">负载预测</div>
                    <div class="item-value digital-twin">85.3%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">故障概率</div>
                    <div class="item-value digital-twin">0.2%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">电压稳定性预测</div>
                    <div class="item-value digital-twin">优</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">检修倒计时</div>
                    <div class="item-value predicted">120天</div>
                  </div>
                </div>
                
                <!-- 水泵看板 -->
                <div v-if="selectedDevice.id === 'waterPump'" class="dashboard-grid">
                  <div class="dashboard-item">
                    <div class="item-label">水泵出口压力</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '0.45MPa' : '0MPa' }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">水泵流量</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '13.58m³/h' : '0m³/h' }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">总流量</div>
                    <div class="item-value measured">44991.008m³</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">水泵状态</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '运行中' : '已停机' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">累计运行时间</div>
                    <div class="item-value measured">1682.5h</div>
                  </div>
                  <!-- 数字孪生数据 -->
                  <div class="dashboard-item">
                    <div class="item-label">效率预测</div>
                    <div class="item-value digital-twin">92.5%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">故障概率</div>
                    <div class="item-value digital-twin">0.8%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">剩余寿命预测</div>
                    <div class="item-value digital-twin">5280h</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">检修倒计时</div>
                    <div class="item-value predicted">45天</div>
                  </div>
                </div>
                
                <!-- 风冷式设备看板 -->
                <div v-if="selectedDevice.id === 'airCooler'" class="dashboard-grid">
                  <div class="dashboard-item">
                    <div class="item-label">进风温度</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '28.5℃' : '环境温度' }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">出风温度</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '18.2℃' : '环境温度' }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">风机转速</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '1450rpm' : '0rpm' }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">风机电流</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '12.8A' : '0A' }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">设备状态</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '运行中' : '已停机' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">制冷量</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '120kW' : '0kW' }}</div>
                  </div>
                  <!-- 数字孪生数据 -->
                  <div class="dashboard-item">
                    <div class="item-label">效率预测</div>
                    <div class="item-value digital-twin">89.7%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">能耗预测</div>
                    <div class="item-value digital-twin">145kWh/日</div>
                  </div>

                  <div class="dashboard-item">
                    <div class="item-label">故障概率</div>
                    <div class="item-value digital-twin">1.5%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">滤网更换提醒</div>
                    <div class="item-value predicted">23天</div>
                  </div>                  
                </div>
                
                <!-- 管道节点1看板 -->
                <div v-if="selectedDevice.id === 'pipeNode1'" class="dashboard-grid">
                  <div class="dashboard-item">
                    <div class="item-label">流量</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '12.5 m³/h' : '0 m³/h' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">流速</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '1.8 m/s' : '0 m/s' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">压力</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '1.2 MPa' : '0 MPa' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">泄露预测</div>
                    <div class="item-value digital-twin">0.0 m³/min</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">距离管道检修</div>
                    <div class="item-value predicted">305 天</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">距离管道清洗</div>
                    <div class="item-value predicted">265 天</div>
                  </div>
                </div>
                
                <!-- 管道节点2看板 -->
                <div v-if="selectedDevice.id === 'pipeNode2'" class="dashboard-grid">
                  <div class="dashboard-item">
                    <div class="item-label">流量</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '10.8 m³/h' : '0 m³/h' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">流速</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '1.5 m/s' : '0 m/s' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">压力</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '1.0 MPa' : '0 MPa' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">泄露预测</div>
                    <div class="item-value digital-twin">0.0 m³/min</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">距离管道检修</div>
                    <div class="item-value predicted">305 天</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">距离管道清洗</div>
                    <div class="item-value predicted">265 天</div>
                  </div>
                </div>
                
                <!-- 管道节点3看板 -->
                <div v-if="selectedDevice.id === 'pipeNode3'" class="dashboard-grid">
                  <div class="dashboard-item">
                    <div class="item-label">流量</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '11.2 m³/h' : '0 m³/h' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">流速</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '1.6 m/s' : '0 m/s' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">压力</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '1.1 MPa' : '0 MPa' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">泄露预测</div>
                    <div class="item-value digital-twin">0.0 m³/min</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">距离管道检修</div>
                    <div class="item-value predicted">305 天</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">距离管道清洗</div>
                    <div class="item-value predicted">265 天</div>
                  </div>
                </div>
                
                <!-- 水泵2看板 -->
                <div v-if="selectedDevice.id === 'waterPump2'" class="dashboard-grid">
                  <div class="dashboard-item">
                    <div class="item-label">水泵出口压力</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '0.42MPa' : '0MPa' }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">水泵流量</div>
                    <div class="item-value measured">{{ currentSystemState === 'running' ? '13.58m³/h' : '0m³/h' }}</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">总流量</div>
                    <div class="item-value measured">44991.008m³</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">水泵状态</div>
                    <div class="item-value measured" :class="currentSystemState === 'running' ? 'normal' : 'abnormal'">
                      {{ currentSystemState === 'running' ? '运行中' : '已停机' }}
                    </div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">累计运行时间</div>
                    <div class="item-value measured">1265.8h</div>
                  </div>
                  <!-- 数字孪生数据 -->
                  <div class="dashboard-item">
                    <div class="item-label">效率预测</div>
                    <div class="item-value digital-twin">91.2%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">故障概率</div>
                    <div class="item-value digital-twin">1.2%</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">剩余寿命预测</div>
                    <div class="item-value digital-twin">6120h</div>
                  </div>
                  <div class="dashboard-item">
                    <div class="item-label">检修倒计时</div>
                    <div class="item-value predicted">58天</div>
                  </div>
                </div>
              </div>
              <div class="dashboard-footer">
                <div class="update-time">更新时间: {{ currentTime }}</div>
              </div>
            </div>
          </div>
          <!-- 模型加载提示 -->
          <div v-if="!model" ref="loadingIndicator" style="
            width: 100%; 
            height: 100%; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            justify-content: center; 
            position: absolute; 
            background-color: #f8fafc;
            color: #2c3e50; 
            font-size: 18px; 
            font-weight: 600;
            border-radius: 10px;
            box-shadow: inset 0 0 20px rgba(66, 133, 244, 0.1);
          ">
            <div style="margin-bottom: 20px; text-align: center;">
              <div style="font-size: 24px; color: #4285f4; margin-bottom: 8px;">🔧</div>
              <div>3D模型加载中，请稍候...</div>
            </div>
            <div style="width: 300px; height: 20px; background-color: rgba(66, 133, 244, 0.1); border-radius: 10px; overflow: hidden; position: relative; border: 1px solid rgba(66, 133, 244, 0.2);">
              <div 
                style="
                  height: 100%; 
                  background: linear-gradient(90deg, #4285f4, #3367d6); 
                  transition: width 0.3s ease;
                  border-radius: 10px;
                "
                :style="{ width: modelLoadingProgress + '%' }"
              ></div>
              <!-- 百分比文本显示在进度条外部，确保始终完整可见 -->
              <div style="
                position: absolute;
                top: 50%;
                left: 50%;
                transform: translate(-50%, -50%);
                color: #4285f4;
                font-size: 12px;
                font-weight: bold;
                pointer-events: none;
                text-shadow: 0 1px 2px rgba(255, 255, 255, 0.8);
                min-width: 40px;
                text-align: center;
              ">
                {{ Math.round(modelLoadingProgress) }}%
              </div>
            </div>
            <div style="margin-top: 15px; font-size: 12px; color: #5f6368; text-align: center;">
              正在加载冷热电联供系统3D模型...
            </div>
          </div>
          
          <!-- 顶层数据点层 -->
          <!-- <div class="data-points-overlay"> -->
            <!-- 冷却水供水温度 -->
            <!-- <div class="data-value-display" style="left: 69%; top: 43%;" :class="{ 'alert': currentSystemState === 'shutdown', 'running': currentSystemState === 'running' }">
              <div class="data-value">{{ systemData[currentSystemState].lithium.coolInTemp }}</div>
            </div> -->

            <!-- 冷却水回水温度 -->
            <!-- <div class="data-value-display" style="left: 75%; top: 43%;" :class="{ 'alert': currentSystemState === 'shutdown', 'running': currentSystemState === 'running' }">
              <div class="data-value">{{ systemData[currentSystemState].lithium.coolOutTemp }}</div>
            </div> -->

            <!-- 冷水供水温度 -->
            <!-- <div class="data-value-display" style="left: 61%; top: 55%;" :class="{ 'alert': currentSystemState === 'shutdown', 'running': currentSystemState === 'running' }">
              <div class="data-value">{{ systemData[currentSystemState].lithium.coldInTemp }}</div>
            </div> -->

            <!-- 冷水回水温度 -->
            <!-- <div class="data-value-display" style="left: 66%; top: 55%;" :class="{ 'alert': currentSystemState === 'shutdown', 'running': currentSystemState === 'running' }">
              <div class="data-value">{{ systemData[currentSystemState].lithium.coldOutTemp }}</div>
            </div> -->

            <!-- 烟气进口温度 -->
            <!-- <div class="data-value-display" style="left: 54%; top: 40.5%;" :class="{ 'alert': currentSystemState === 'shutdown', 'running': currentSystemState === 'running' }">
              <div class="data-value">{{ systemData[currentSystemState].lithium.smokeInTemp }}</div>
            </div> -->

            <!-- 烟气出口温度 -->
            <!-- <div class="data-value-display" style="left: 54%; top: 30.5%;" :class="{ 'alert': currentSystemState === 'shutdown', 'running': currentSystemState === 'running' }">
              <div class="data-value">{{ systemData[currentSystemState].lithium.smokeOutTemp }}</div>
            </div> -->

            <!-- 发电机参数 -->
            <!-- <div class="data-value-display" style="left: 28%; top: 50%;" :class="{ 'alert': currentSystemState === 'shutdown', 'running': currentSystemState === 'running' }">
              <div class="data-value" style="display: flex; justify-content: space-around; gap: 20px;"> 发电Uab <span style="text-align: right;">{{systemData[currentSystemState].generator.Uab }}</span></div>
              <div class="data-value" style="display: flex; justify-content: space-around; gap: 20px;"> A相电流 <span style="text-align: right;">{{ systemData[currentSystemState].generator.currentA }}</span></div>
              <div class="data-value" style="display: flex; justify-content: space-between; gap: 20px;">总有功功率 <span style="text-align: right;">{{ systemData[currentSystemState].generator.powerTotal }}</span></div>
              <div class="data-value" style="display: flex; justify-content: space-between; gap: 20px;">总无功功率 <span style="text-align: right;">{{ systemData[currentSystemState].generator.reactiveTotal }}</span></div>
            </div> -->
          <!-- </div> -->
        </div>
        
        <!-- <div class="placeholder-image" v-else>
          <span>系统停机中，启动后显示设备运行画面</span>
        </div> -->
      </div>
      
      <!-- 右侧控制区 -->
      <div class="right-controls">
        <!-- 右侧按钮组 -->
        <div class="right-buttons">
            <button class="dashboard-button secondary" :class="{ active: currentSystemState === 'running' }" @click="setSystemState('running')">
              <span class="button-icon">▶</span>
              <span>系统启动</span>
            </button>
            <button class="dashboard-button secondary" :class="{ active: currentSystemState === 'shutdown' }" @click="setSystemState('shutdown')">
              <span class="button-icon">◼</span>
              <span>系统停机</span>
            </button>
            <button class="dashboard-button secondary" @click="handleFaultReset">
              <span class="button-icon">🔄</span>
              <span>故障复位</span>
            </button>
        </div>
      </div>
    </div>
    
    <!-- 底部区域 -->
    <div class="bottom-section">
      <!-- 左侧数据列表区域（占1/2） -->
      <div class="bottom-left">
        <div class="data-lists-container">
          <!-- 燃气发电机数据（含市电+发电参数） -->
          <div class="data-list">
            <h3 class="list-title">燃气发电机信息</h3>
            <div class="data-item">
              <span class="data-label">型号/转速</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.model }}/{{ systemData[currentSystemState].generator.speed }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">流量/扬程</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.flowRate }}/{{ systemData[currentSystemState].generator.head }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">汽蚀余量/电压</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.npsh }}/{{ systemData[currentSystemState].generator.voltage }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">频率/压力</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.frequency }}/{{ systemData[currentSystemState].generator.pressure }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">电流/功率</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.current }}/{{ systemData[currentSystemState].generator.power }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">热分级/启动电流</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.thermalClass }}/{{ systemData[currentSystemState].generator.startingCurrent }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">相数/最高温度</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.phase }}/{{ systemData[currentSystemState].generator.maxTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">防护等级/重量</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.protectionClass }}/{{ systemData[currentSystemState].generator.weight }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">出厂日期</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.factoryDate }}</span>
            </div>
            
            <h3 class="list-title">实时运行数据</h3>
            <!-- 市电电压 -->
            <div class="data-item">
              <span class="data-label">市电Uab</span>
              <span class="data-value">{{ systemData[currentSystemState].powerGrid.Uab }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">市电Ubc</span>
              <span class="data-value">{{ systemData[currentSystemState].powerGrid.Ubc }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">市电Uca</span>
              <span class="data-value">{{ systemData[currentSystemState].powerGrid.Uca }}</span>
            </div>
            <!-- 发电参数 -->
            <div class="data-item">
              <span class="data-label">发电Uab</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.Uab }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">A相电流</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.currentA }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">总有功功率</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.powerTotal }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">总无功功率</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.reactiveTotal }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">发电频率</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.frequency }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">发电机转速</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.speed }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">排气温度</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.exhaustTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">并网开关状态</span>
              <span class="data-value" :class="systemData[currentSystemState].generator.gridSwitch === '合闸' ? 'normal' : 'abnormal'">
                {{ systemData[currentSystemState].generator.gridSwitch }}
              </span>
            </div>
            
            <h3 class="list-title">数字孪生数据</h3>
            <div class="data-item">
              <span class="data-label">效率预测</span>
              <span class="data-value" :class="systemData[currentSystemState].generator.efficiencyPredict.includes('9') ? 'normal' : 'warning'">
                {{ systemData[currentSystemState].generator.efficiencyPredict }}
              </span>
            </div>
            <div class="data-item">
              <span class="data-label">检修倒计时</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.maintenanceCountdown }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">故障风险</span>
              <span class="data-value" :class="systemData[currentSystemState].generator.faultRisk === '低' ? 'normal' : 'abnormal'">
                {{ systemData[currentSystemState].generator.faultRisk }}
              </span>
            </div>
            <div class="data-item">
              <span class="data-label">剩余寿命预测</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.lifetimePredict }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">振动水平</span>
              <span class="data-value" :class="systemData[currentSystemState].generator.vibrationLevel === '正常' ? 'normal' : 'warning'">
                {{ systemData[currentSystemState].generator.vibrationLevel }}
              </span>
            </div>
            <div class="data-item">
              <span class="data-label">噪音水平</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.noiseLevel }}</span>
            </div>
            <div class="data-item" v-if="currentSystemState === 'running'">
              <span class="data-label">性能趋势</span>
              <span class="data-value" :class="systemData[currentSystemState].generator.performanceTrend === '良好' ? 'normal' : 'warning'">
                {{ systemData[currentSystemState].generator.performanceTrend }}
              </span>
            </div>
            <div class="data-item" v-if="currentSystemState === 'running'">
              <span class="data-label">节能率</span>
              <span class="data-value" :class="systemData[currentSystemState].generator.energySavingRate.includes('12') ? 'normal' : 'warning'">
                {{ systemData[currentSystemState].generator.energySavingRate }}
              </span>
            </div>
          </div>
          <!-- 溴化锂机组数据 -->
          <div class="data-list">
            <h3 class="list-title">溴化锂机组信息</h3>
            <div class="data-item">
              <span class="data-label">机组型号</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.model }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">制冷/制热量</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.coolingCapacity }}/{{ systemData[currentSystemState].lithium.heatingCapacity }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">冷/热水流量</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.coldWaterFlow }}/{{ systemData[currentSystemState].lithium.hotWaterFlow }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">冷/热水出口温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.coldWaterTemp }}/{{ systemData[currentSystemState].lithium.hotWaterTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">冷却水流量/进口温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.coolingWaterFlow }}/{{ systemData[currentSystemState].lithium.coolingWaterInTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">烟气耗量/进口温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.exhaustGasConsumption }}/{{ systemData[currentSystemState].lithium.exhaustGasInTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">配电量/电源</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.powerConsumption }}/{{ systemData[currentSystemState].lithium.powerSupply }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">生产编号/制造日期</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.productionNumber }}/{{ systemData[currentSystemState].lithium.manufactureDate }}</span>
            </div>
            
            <h3 class="list-title">实时运行数据</h3>
            <div class="data-item">
              <span class="data-label">冷水供水温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.coldInTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">冷水回水温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.coldOutTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">烟气进口温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.smokeInTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">烟气出口温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.smokeOutTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">冷却水供水温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.coolInTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">冷却水回水温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.coolOutTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">蒸发器温度</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.evaporatorTemp }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">蒸发器压力</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.evaporatorPress }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">溴化锂启动状态</span>
              <span class="data-value" :class="systemData[currentSystemState].lithium.startState === '启动' ? 'normal' : 'abnormal'">
                {{ systemData[currentSystemState].lithium.startState }}
              </span>
            </div>
            
            <h3 class="list-title">数字孪生数据</h3>
            <div class="data-item">
              <span class="data-label">效率预测</span>
              <span class="data-value" :class="systemData[currentSystemState].lithium.efficiencyPredict.includes('9') ? 'normal' : 'warning'">
                {{ systemData[currentSystemState].lithium.efficiencyPredict }}
              </span>
            </div>
            <div class="data-item">
              <span class="data-label">检修倒计时</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.maintenanceCountdown }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">故障风险</span>
              <span class="data-value" :class="systemData[currentSystemState].lithium.faultRisk === '低' ? 'normal' : 'abnormal'">
                {{ systemData[currentSystemState].lithium.faultRisk }}
              </span>
            </div>
            <div class="data-item">
              <span class="data-label">剩余寿命预测</span>
              <span class="data-value">{{ systemData[currentSystemState].lithium.lifetimePredict }}</span>
            </div>
            <div class="data-item" v-if="currentSystemState === 'running'">
              <span class="data-label">性能趋势</span>
              <span class="data-value" :class="systemData[currentSystemState].lithium.performanceTrend === '良好' ? 'normal' : 'warning'">
                {{ systemData[currentSystemState].lithium.performanceTrend }}
              </span>
            </div>
            <div class="data-item" v-if="currentSystemState === 'running'">
              <span class="data-label">节能率</span>
              <span class="data-value" :class="systemData[currentSystemState].lithium.energySavingRate.includes('15') ? 'normal' : 'warning'">
                {{ systemData[currentSystemState].lithium.energySavingRate }}
              </span>
            </div>
            <!-- 累计数据 -->
            <div class="data-item">
              <span class="data-label">累计发电量</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.totalPower }}</span>
            </div>
            <div class="data-item">
              <span class="data-label">累计燃气量</span>
              <span class="data-value">{{ systemData[currentSystemState].generator.totalGas }}</span>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 右侧图表区域（占1/2） -->
      <div class="bottom-right">

        <!-- 圆形图表区域（动态绑定启动/停机数据） -->
        <div class="gauge-charts-container">
          <!-- 发电机电压 -->
          <div class="gauge-chart">
            <div class="gauge-title">发电Uab</div>
            <div class="gauge-circle" :style="{'--progress': `calc((${systemData[currentSystemState].generator.UabValue} / 450) * 100%)`}">
              <div class="gauge-progress"></div>
              <div class="gauge-value">{{ systemData[currentSystemState].generator.Uab }}</div>
            </div>
          </div>
          <!-- 发电机电流 -->
          <div class="gauge-chart">
            <div class="gauge-title">A相电流</div>
            <div class="gauge-circle" :style="{'--progress': `calc((${systemData[currentSystemState].generator.currentAValue} / 200) * 100%)`}">
              <div class="gauge-progress"></div>
              <div class="gauge-value">{{ systemData[currentSystemState].generator.currentA }}</div>
            </div>
          </div>
          <!-- 总有功功率 -->
          <div class="gauge-chart">
            <div class="gauge-title">总有功功率</div>
            <div class="gauge-circle" :style="{'--progress': `calc((${systemData[currentSystemState].generator.powerTotalValue} / 150) * 100%)`}">
              <div class="gauge-progress"></div>
              <div class="gauge-value">{{ systemData[currentSystemState].generator.powerTotal }}</div>
            </div>
          </div>
          <!-- 发电频率 -->
          <div class="gauge-chart">
            <div class="gauge-title">发电频率</div>
            <div class="gauge-circle" :style="{'--progress': `calc((${systemData[currentSystemState].generator.frequencyValue} / 52) * 100%)`}">
              <div class="gauge-progress"></div>
              <div class="gauge-value">{{ systemData[currentSystemState].generator.frequency }}</div>
            </div>
          </div>
          <!-- 冷水供水温度 -->
          <div class="gauge-chart">
            <div class="gauge-title">冷水供水温度</div>
            <div class="gauge-circle" :style="{'--progress': `calc((${systemData[currentSystemState].lithium.coldInTempValue} / 30) * 100%)`}">
              <div class="gauge-progress"></div>
              <div class="gauge-value">{{ systemData[currentSystemState].lithium.coldInTemp }}</div>
            </div>
          </div>
          <!-- 烟气进口温度 -->
          <div class="gauge-chart">
            <div class="gauge-title">烟气进口温度</div>
            <div class="gauge-circle" :style="{'--progress': `calc((${systemData[currentSystemState].lithium.smokeInTempValue} / 300) * 100%)`}">
              <div class="gauge-progress"></div>
              <div class="gauge-value">{{ systemData[currentSystemState].lithium.smokeInTemp }}</div>
            </div>
          </div>
        </div>
        <!-- 趋势图区域（新增横纵坐标+数据点标识） -->
        <div class="trend-charts-container">
          <div class="trend-chart-row">
            <!-- 1. 发电机电流趋势图（新增坐标+数据点） -->
            <div class="trend-chart">
              <div class="trend-title">发电机电流趋势（A）</div>
              <div class="trend-plot">
                <svg width="100%" height="100%" viewBox="0 0 450 180">
                  <!-- 坐标轴：X轴（时间）、Y轴（电流） -->
                  <g class="axis">
                    <!-- X轴轴线 -->
                    <line x1="40" y1="150" x2="420" y2="150" stroke="#b0c4de" stroke-width="1.5"/>
                    <!-- X轴刻度（时间：0-60分钟，每15分钟1个刻度） -->
                    <template v-for="(x, idx) in [0,15,30,45,60]" :key="idx">
                      <line :x1="40 + idx*95" y1="145" :x2="40 + idx*95" y2="155" stroke="#b0c4de" stroke-width="1.5"/>
                      <text :x="40 + idx*95" y="170" fill="#b0c4de" font-size="11" text-anchor="middle">{{ x }}min</text>
                    </template>
                    <!-- Y轴轴线 -->
                    <line x1="40" y1="30" x2="40" y2="150" stroke="#b0c4de" stroke-width="1.5"/>
                    <!-- Y轴刻度（电流：0-200A，每50A1个刻度） -->
                    <template v-for="(y, idx) in [0,50,100,150,200]" :key="idx">
                      <line x1="35" :y1="150 - idx*30" x2="45" :y2="150 - idx*30" stroke="#b0c4de" stroke-width="1.5"/>
                      <text x="30" :y="153 - idx*30" fill="#b0c4de" font-size="11" text-anchor="end">{{ y }}A</text>
                    </template>
                  </g>

                  <!-- 运行状态曲线+数据点 -->
                  <g v-if="currentSystemState === 'running'">
                    <!-- 趋势曲线 - 动态跟随实时数据 -->
                    <path :d="`M40,${currentGeneratorCurrentY} Q135,${currentGeneratorCurrentY - 2.5} 230,${currentGeneratorCurrentY + 3.5} T420,${currentGeneratorCurrentY - 1.5}`" fill="none" stroke="#00bfff" stroke-width="2.5"/>
                    <!-- 数据点（5个关键节点） - 动态跟随实时数据 -->
                    <template v-for="(offset, idx) in [0, -2.5, 3.5, 0.5, -1.5]" :key="idx">
                      <circle :cx="40 + idx*95" :cy="currentGeneratorCurrentY + offset" r="4" fill="#00bfff" stroke="#fff" stroke-width="1"/>
                      <text :x="40 + idx*95" :y="currentGeneratorCurrentY + offset - 8" fill="#00bfff" font-size="10" text-anchor="middle">{{ idx === 0 ? systemData.running.generator.currentAValue.toFixed(1) : (systemData.running.generator.currentAValue + (offset * 0.6)).toFixed(1) }}A</text>
                    </template>
                  </g>

                  <!-- 停机状态曲线+数据点（固定0A） -->
                  <g v-else>
                    <path d="M40,150 Q135,150 230,150 T420,150" fill="none" stroke="#ff6b6b" stroke-width="2.5"/>
                    <template v-for="idx in [0,1,2,3,4]" :key="idx">
                      <circle :cx="40 + idx*95" cy="150" r="4" fill="#ff6b6b" stroke="#fff" stroke-width="1"/>
                      <text :x="40 + idx*95" y="142" fill="#ff6b6b" font-size="10" text-anchor="middle">0.0A</text>
                    </template>
                  </g>
                </svg>
              </div>
            </div>

            <!-- 2. 总有功功率趋势图（新增坐标+数据点） -->
            <div class="trend-chart">
              <div class="trend-title">总有功功率趋势（kW）</div>
              <div class="trend-plot">
                <svg width="100%" height="100%" viewBox="0 0 450 180">
                  <!-- 坐标轴：X轴（时间）、Y轴（功率） -->
                  <g class="axis">
                    <line x1="40" y1="150" x2="420" y2="150" stroke="#b0c4de" stroke-width="1.5"/>
                    <template v-for="(x, idx) in [0,15,30,45,60]" :key="idx">
                      <line :x1="40 + idx*95" y1="145" :x2="40 + idx*95" y2="155" stroke="#b0c4de" stroke-width="1.5"/>
                      <text :x="40 + idx*95" y="170" fill="#b0c4de" font-size="11" text-anchor="middle">{{ x }}min</text>
                    </template>
                    <line x1="40" y1="30" x2="40" y2="150" stroke="#b0c4de" stroke-width="1.5"/>
                    <template v-for="(y, idx) in [0,37.5,75,112.5,150]" :key="idx">
                      <line x1="35" :y1="150 - idx*30" x2="45" :y2="150 - idx*30" stroke="#b0c4de" stroke-width="1.5"/>
                      <text x="30" :y="153 - idx*30" fill="#b0c4de" font-size="11" text-anchor="end">{{ y }}kW</text>
                    </template>
                  </g>

                  <!-- 运行状态曲线+数据点 -->
                  <g v-if="currentSystemState === 'running'">
                    <!-- 趋势曲线 - 动态跟随实时数据 -->
                    <path :d="`M40,${currentTotalPowerY} Q135,${currentTotalPowerY - 6} 230,${currentTotalPowerY - 2} T420,${currentTotalPowerY - 13}`" fill="none" stroke="#32cd32" stroke-width="2.5"/>
                    <!-- 数据点（5个关键节点） - 动态跟随实时数据 -->
                    <template v-for="(offset, idx) in [0, -6, -2, -8, -13]" :key="idx">
                      <circle :cx="40 + idx*95" :cy="currentTotalPowerY + offset" r="4" fill="#32cd32" stroke="#fff" stroke-width="1"/>
                      <text :x="40 + idx*95" :y="currentTotalPowerY + offset - 8" fill="#32cd32" font-size="10" text-anchor="middle">{{ idx === 0 ? systemData.running.generator.powerTotalValue.toFixed(1) : (systemData.running.generator.powerTotalValue + (offset * 0.1)).toFixed(1) }}kW</text>
                    </template>
                  </g>

                  <!-- 停机状态曲线+数据点（固定0kW） -->
                  <g v-else>
                    <path d="M40,150 Q135,150 230,150 T420,150" fill="none" stroke="#ff6b6b" stroke-width="2.5"/>
                    <template v-for="idx in [0,1,2,3,4]" :key="idx">
                      <circle :cx="40 + idx*95" cy="150" r="4" fill="#ff6b6b" stroke="#fff" stroke-width="1"/>
                      <text :x="40 + idx*95" y="142" fill="#ff6b6b" font-size="10" text-anchor="middle">0.0kW</text>
                    </template>
                  </g>
                </svg>
              </div>
            </div>
          </div>

          <div class="trend-chart-row">
            <!-- 3. 冷水供水温度趋势图（新增坐标+数据点） -->
            <div class="trend-chart">
              <div class="trend-title">冷水供水温度趋势（℃）</div>
              <div class="trend-plot">
                <svg width="100%" height="100%" viewBox="0 0 450 180">
                  <!-- 坐标轴：X轴（时间）、Y轴（温度） -->
                  <g class="axis">
                    <line x1="40" y1="150" x2="420" y2="150" stroke="#b0c4de" stroke-width="1.5"/>
                    <template v-for="(x, idx) in [0,15,30,45,60]" :key="idx">
                      <line :x1="40 + idx*95" y1="145" :x2="40 + idx*95" y2="155" stroke="#b0c4de" stroke-width="1.5"/>
                      <text :x="40 + idx*95" y="170" fill="#b0c4de" font-size="11" text-anchor="middle">{{ x }}min</text>
                    </template>
                    <line x1="40" y1="30" x2="40" y2="150" stroke="#b0c4de" stroke-width="1.5"/>
                    <template v-for="(y, idx) in [0,7.5,15,22.5,30]" :key="idx">
                      <line x1="35" :y1="150 - idx*30" x2="45" :y2="150 - idx*30" stroke="#b0c4de" stroke-width="1.5"/>
                      <text x="30" :y="153 - idx*30" fill="#b0c4de" font-size="11" text-anchor="end">{{ y }}℃</text>
                    </template>
                  </g>

                  <!-- 运行状态曲线+数据点（动态跟随实时数据） -->
                  <g v-if="currentSystemState === 'running'">
                    <path :d="`M40,${currentColdTempY} Q135,${currentColdTempY - 4} 230,${currentColdTempY - 1} T420,${currentColdTempY - 7}`" fill="none" stroke="#00bfff" stroke-width="2.5"/>
                    <template v-for="(offset, idx) in [0, -4, -1, -3, -7]" :key="idx">
                      <circle :cx="40 + idx*95" :cy="currentColdTempY + offset" r="4" fill="#00bfff" stroke="#fff" stroke-width="1"/>
                      <text :x="40 + idx*95" :y="currentColdTempY + offset - 8" fill="#00bfff" font-size="10" text-anchor="middle">{{ idx === 0 ? systemData.running.lithium.coldInTempValue.toFixed(1) : (systemData.running.lithium.coldInTempValue + (offset * 0.1)).toFixed(1) }}℃</text>
                    </template>
                  </g>

                  <!-- 停机状态曲线+数据点（贴合Excel停机值17.8℃） -->
                  <g v-else>
                    <path d="M40,58 Q135,58 230,58 T420,58" fill="none" stroke="#ff6b6b" stroke-width="2.5"/>
                    <template v-for="idx in [0,1,2,3,4]" :key="idx">
                      <circle :cx="40 + idx*95" cy="58" r="4" fill="#ff6b6b" stroke="#fff" stroke-width="1"/>
                      <text :x="40 + idx*95" y="50" fill="#ff6b6b" font-size="10" text-anchor="middle">18.0℃</text>
                    </template>
                  </g>
                </svg>
              </div>
            </div>

            <!-- 4. 烟气进口温度趋势图（新增坐标+数据点） -->
            <div class="trend-chart">
              <div class="trend-title">烟气进口温度趋势（℃）</div>
              <div class="trend-plot">
                <svg width="100%" height="100%" viewBox="0 0 450 180">
                  <!-- 坐标轴：X轴（时间）、Y轴（温度） -->
                  <g class="axis">
                    <line x1="40" y1="150" x2="420" y2="150" stroke="#b0c4de" stroke-width="1.5"/>
                    <template v-for="(x, idx) in [0,15,30,45,60]" :key="idx">
                      <line :x1="40 + idx*95" y1="145" :x2="40 + idx*95" y2="155" stroke="#b0c4de" stroke-width="1.5"/>
                      <text :x="40 + idx*95" y="170" fill="#b0c4de" font-size="11" text-anchor="middle">{{ x }}min</text>
                    </template>
                    <line x1="40" y1="30" x2="40" y2="150" stroke="#b0c4de" stroke-width="1.5"/>
                    <template v-for="(y, idx) in [270,280,290,300,310]" :key="idx">
                      <line x1="35" :y1="150 - (idx*30)" x2="45" :y2="150 - (idx*30)" stroke="#b0c4de" stroke-width="1.5"/>
                      <text x="30" :y="153 - (idx*30)" fill="#b0c4de" font-size="11" text-anchor="end">{{ y }}℃</text>
                    </template>
                  </g>

                  <!-- 运行状态曲线+数据点（动态跟随实时数据） -->
                  <g v-if="currentSystemState === 'running'">
                    <path :d="`M40,${currentSmokeTempY} Q135,${currentSmokeTempY - 3} 230,${currentSmokeTempY - 1} T420,${currentSmokeTempY - 7}`" fill="none" stroke="#ff6347" stroke-width="2.5"/>
                    <template v-for="(offset, idx) in [0, -3, -1, -5, -7]" :key="idx">
                      <circle :cx="40 + idx*95" :cy="currentSmokeTempY + offset" r="4" fill="#ff6347" stroke="#fff" stroke-width="1"/>
                      <text :x="40 + idx*95" :y="currentSmokeTempY + offset - 8" fill="#ff6347" font-size="10" text-anchor="middle">{{ idx === 0 ? systemData.running.lithium.smokeInTempValue.toFixed(1) : (systemData.running.lithium.smokeInTempValue + (offset * 0.25)).toFixed(1) }}℃</text>
                    </template>
                  </g>

                  <!-- 停机状态曲线+数据点（贴合Excel停机值18.0℃） -->
                  <g v-else>
                    <path d="M40,150 Q135,150 230,150 T420,150" fill="none" stroke="#ff6b6b" stroke-width="2.5"/>
                    <template v-for="idx in [0,1,2,3,4]" :key="idx">
                      <circle :cx="40 + idx*95" cy="150" r="4" fill="#ff6b6b" stroke="#fff" stroke-width="1"/>
                      <text :x="40 + idx*95" y="142" fill="#ff6b6b" font-size="10" text-anchor="middle">18.0℃</text>
                    </template>
                  </g>
                </svg>
              </div>
            </div>
          </div>
        </div>
          
        </div>
      </div>
    </div>
</template>
<script>
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import { markRaw } from 'vue';
export default {
  name: 'DataDashboard',
  data() {
    return {
      currentDate: '',
      currentTime: '',
      // 当前系统状态：shutdown（停机）/ running（运行）
      currentSystemState: 'running',
      // 每个数据点独立的显示状态，默认都隐藏
      dataPointsVisibility: {
        coolingWaterSupplyTemperature: true,
        coolingWaterReturnTemperature: true,
        coldWaterSupplyTemperature: true,
        coldWaterReturnTemperature: true,
        hotWaterSupplyTemperature: true,
        hotWaterReturnTemperature: true,
        generatorVoltage: true
      },
      // 模型加载进度
      modelLoadingProgress: 0,
      // 3D模型对象
      model: null,
      // Three.js对象
      scene: null,
      camera: null,
      renderer: null,
      animationId: null,
      // 设备信息按钮数据
      deviceButtons: [
        {
          id: 'generator',
          name: '燃气发电机',
          position: { x: -3, y: 0.1, z: 1.5 },
          visible: true,
          dataSource: 'generator'
        },
        {
          id: 'lithium',
          name: '溴化锂机组',
          position: { x: -3, y: 0.1, z: -1.5 },
          visible: true,
          dataSource: 'lithium'
        },
        {
          id: 'powerGrid',
          name: '电网系统',
          position: { x: -1, y: 0.1, z: 5 },
          visible: true,
          dataSource: 'powerGrid'
        },
        {
          id: 'waterPump',
          name: '水泵1',
          position: { x: 2, y: 0.1, z: 3 },
          visible: true,
          dataSource: 'waterPump'
        },
        {
          id: 'airCooler',
          name: '风冷式设备',
          position: { x: -4.5, y: 0.1, z: -10 },
          visible: true,
          dataSource: 'airCooler'
        },
        // 管道关键节点按钮
        {
          id: 'pipeNode1',
          name: '管道节点1',
          position: { x: 1, y: 0.1, z: 0},
          visible: true,
          dataSource: 'pipeNode1'
        },
        {
          id: 'pipeNode2',
          name: '管道节点2',
          position: { x: 0, y: 0.1, z: -2 },
          visible: true,
          dataSource: 'pipeNode2'
        },
        {
          id: 'pipeNode3',
          name: '管道节点3',
          position: { x: -1, y: 0.1, z: -7 },
          visible: true,
          dataSource: 'pipeNode3'
        },
        // 新水泵按钮
        {
          id: 'waterPump2',
          name: '水泵2',
          position: { x: 0, y: 0.1, z: -4.5 },
          visible: true,
          dataSource: 'waterPump2'
        }
      ],
      // 当前选中的设备
      selectedDevice: null,
      // 看板显示状态
      dashboardVisible: false,

    // 趋势图的基础配置
    trendChartConfig: {
      // SVG视图框大小
      viewBox: {
        width: 450,
        height: 180
      },
      // 坐标轴范围
      axes: {
        // 时间轴范围（分钟）
        time: {
          min: 0,
          max: 60
        },
        // 各个参数的Y轴范围
        generatorCurrent: {
          min: 0,
          max: 200
        },
        totalPower: {
          min: 0,
          max: 150
        },
        coldTemp: {
          min: 0,
          max: 30
        },
        hotTemp: {
          min: 0,
          max: 300
        }
      }
    },
      // 两套数据：严格对应「画面数据.xlsx」
      systemData: {
        // 系统停机数据
        shutdown: {
          powerGrid: {
            Uab: '398.9v',
            Ubc: '401.5v',
            Uca: '399.9v'
          },
          generator: {
            model: 'MODEL-NP89',
            speed: '2900 r/min',
            flowRate: '7m³/h',
            head: '14m',
            npsh: '0.15',
            voltage: '380v',
            frequency: '50hz',
            pressure: '0.5mpa',
            current: '6.1a',
            power: '2.2kw',
            thermalClass: 'CL',
            startingCurrent: '23a',
            phase: '3PHASE',
            maxTemp: '<=110℃',
            protectionClass: 'IP54',
            weight: '48kg',
            factoryDate: '202312',
            Uab: '0.0v',
            UabValue: 0.0,
            currentA: '0.0A',
            currentAValue: 0.0,
            powerTotal: '0.0kw',
            powerTotalValue: 0.0,
            reactiveTotal: '0.0kvar',
           
            frequencyValue: 0.0,

            exhaustTemp: '18.5℃',
            gridSwitch: '分闸',
            totalPower: '129568.5 kwh',
            totalGas: '15234.2 m³',
            // 数字孪生数据
            efficiencyPredict: '94.2%',
            maintenanceCountdown: '2136h',
            faultRisk: '低',
            lifetimePredict: '92300h',
            vibrationLevel: '正常',
            noiseLevel: '65dB'
          },
          lithium: {
            model: 'Y（309/145）-12（32/38）（12/7）',
            coolingCapacity: '120kw',
            heatingCapacity: '85kw',
            coldWaterFlow: '20.6m³/h',
            hotWaterFlow: '14.6m³/h',
            coldWaterTemp: '7℃',
            hotWaterTemp: '60℃',
            coolingWaterFlow: '31.2m³/h',
            coolingWaterInTemp: '32℃',
            exhaustGasConsumption: '1764kg/h',
            exhaustGasInTemp: '309℃',
            powerConsumption: '4.25kw',
            powerSupply: '380v/3ph/50hz',
            productionNumber: 'Y10-02966',
            manufactureDate: '2024.01',
            coldInTemp: '17.8℃',
            coldInTempValue: 17.8,
            coldOutTemp: '18.1℃',
            smokeInTemp: '18.0℃',
            smokeInTempValue: 18.0,
            smokeOutTemp: '17.9℃',
            coolInTemp: '18.2℃',
            coolOutTemp: '17.6℃',
            evaporatorTemp: '16.6℃',
            evaporatorPress: '16.9kPa',
            startState: '启动',
            // 数字孪生数据
            efficiencyPredict: '92.5%',
            maintenanceCountdown: '1286h',
            faultRisk: '低',
            lifetimePredict: '86500h'
          }
        },
        // 系统运行数据
        running: {
          powerGrid: {
            Uab: '401.2v',
            Ubc: '402.5v',
            Uca: '399.5v'
          },
          generator: {
            model: 'MODEL-NP89',
            speed: '2900 r/min',
            flowRate: '7m³/h',
            head: '14m',
            npsh: '0.15',
            voltage: '380v',
            frequency: '50hz',
            pressure: '0.5mpa',
            current: '6.1a',
            power: '2.2kw',
            thermalClass: 'CL',
            startingCurrent: '23a',
            phase: '3PHASE',
            maxTemp: '<=110℃',
            protectionClass: 'IP54',
            weight: '48kg',
            factoryDate: '202312',
            Uab: '401.0v',
            UabValue: 401.0,
            currentA: '80.9A',
            currentAValue: 80.9,
            powerTotal: '55.7kw',
            powerTotalValue: 55.7,
            reactiveTotal: '16.8kvar',

            frequencyValue: 49.9,

            exhaustTemp: '418.5℃',
            gridSwitch: '合闸',
            totalPower: '130012.5 kwh',
            totalGas: '15265.2 m³',
            // 数字孪生数据
            efficiencyPredict: '95.6%',
            maintenanceCountdown: '2135.8h',
            faultRisk: '低',
            lifetimePredict: '92299.6h',
            vibrationLevel: '正常',
            noiseLevel: '68dB',
            performanceTrend: '良好',
            energySavingRate: '12.8%'
          },
          lithium: {
            model: 'Y（309/145）-12（32/38）（12/7）',
            coolingCapacity: '120kw',
            heatingCapacity: '85kw',
            coldWaterFlow: '20.6m³/h',
            hotWaterFlow: '14.6m³/h',
            coldWaterTemp: '7℃',
            hotWaterTemp: '60℃',
            coolingWaterFlow: '31.2m³/h',
            coolingWaterInTemp: '32℃',
            exhaustGasConsumption: '1764kg/h',
            exhaustGasInTemp: '309℃',
            powerConsumption: '4.25kw',
            powerSupply: '380v/3ph/50hz',
            productionNumber: 'Y10-02966',
            manufactureDate: '2024.01',
            coldInTemp: '8.5℃',
            coldInTempValue: 8.5,
            coldOutTemp: '12.6℃',
            smokeInTemp: '288.8℃',
            smokeInTempValue: 288.8,
            smokeOutTemp: '65.5℃',
            smokeOutTempValue: 65.5,
            coolInTemp: '29.0℃',
            coolOutTemp: '25.6℃',
            evaporatorTemp: '6.5℃',
            evaporatorPress: '0.69Mpa',
            startState: '停机',
            // 数字孪生数据
            efficiencyPredict: '93.8%',
            maintenanceCountdown: '1285.5h',
            faultRisk: '低',
            lifetimePredict: '86499.5h',
            performanceTrend: '良好',
            energySavingRate: '15.2%'
          }
        }
      }
    }
  },
  computed: {
    // 计算发电机电流的Y坐标（实时数据转SVG坐标）
    currentGeneratorCurrentY() {
      // 电流值：systemData.running.generator.currentAValue
      // 转换公式：SVG Y坐标 = 坐标轴底部Y坐标 - (电流值/最大值) * 坐标轴高度
      // 坐标轴底部Y坐标：150
      // 坐标轴高度：120 (150-30)
      return 150 - (this.systemData.running.generator.currentAValue / this.trendChartConfig.axes.generatorCurrent.max) * 120;
    },
    // 计算总有功功率的Y坐标（实时数据转SVG坐标）
    currentTotalPowerY() {
      // 功率值：systemData.running.generator.powerTotalValue
      // 转换公式：SVG Y坐标 = 坐标轴底部Y坐标 - (功率值/最大值) * 坐标轴高度
      return 150 - (this.systemData.running.generator.powerTotalValue / this.trendChartConfig.axes.totalPower.max) * 120;
    },
    // 计算冷水供水温度的Y坐标（实时数据转SVG坐标）
    currentColdTempY() {
      // 温度值：systemData.running.lithium.coldInTempValue
      // 转换公式：SVG Y坐标 = 坐标轴底部Y坐标 - (温度值/最大值) * 坐标轴高度
      return 150 - (this.systemData.running.lithium.coldInTempValue / this.trendChartConfig.axes.coldTemp.max) * 120;
    },
    // 计算烟气进口温度的Y坐标（实时数据转SVG坐标）
    currentSmokeTempY() {
      // 温度值：systemData.running.lithium.smokeInTempValue
      // 转换公式：SVG Y坐标 = 坐标轴底部Y坐标 - (温度值/最大值) * 坐标轴高度
      return 150 - (this.systemData.running.lithium.smokeInTempValue / this.trendChartConfig.axes.hotTemp.max) * 120;
    }
  },
  mounted() {
    // 初始化日期时间
    this.updateDateTime();
    setInterval(() => this.updateDateTime(), 1000);
    
    // 添加实时数据更新定时器，每1分钟更新一次
    setInterval(() => this.updateRealTimeData(), 2000);
    
    // 初始化3D场景
    this.$nextTick(() => {
      this.init3DScene();
    });
  },
  beforeUnmount() {
    // 清理3D场景以避免内存泄漏
    if (this.animationId) {
      cancelAnimationFrame(this.animationId);
    }
    
    // 清理模型
    if (this.model) {
      this.scene.remove(this.model);
      this.model.traverse((child) => {
        if (child.geometry) {
          child.geometry.dispose();
        }
        if (child.material) {
          if (Array.isArray(child.material)) {
            child.material.forEach(material => material.dispose());
          } else {
            child.material.dispose();
          }
        }
      });
    }
    
    // 清理渲染器
    if (this.renderer) {
      this.renderer.dispose();
      if (this.renderer.domElement && this.renderer.domElement.parentNode) {
        this.renderer.domElement.parentNode.removeChild(this.renderer.domElement);
      }
    }
    
    // 移除窗口大小变化监听
    window.removeEventListener('resize', this.handleResize);
    
    // 清理鼠标和键盘事件监听器
    window.removeEventListener('mouseup', this.handleMouseUp);
    window.removeEventListener('keydown', this.handleKeyDown);
    window.removeEventListener('keyup', this.handleKeyUp);
  },
  methods: {
    // 跳转运行优化页面
    handleOptimizationClick() {
      this.$router.push('/optimization');
    },
    // 跳转故障诊断页面
    handleDiagnosisClick() {
      this.$router.push('/fault-diagnosis');
    },

    // 设置系统状态（停机/运行）
    setSystemState(state) {
      this.currentSystemState = state;
      alert(`系统已${state === 'running' ? '启动' : '停机'}，数据已更新`);
    },
    // 故障复位（示例逻辑）
    handleFaultReset() {
      if (this.currentSystemState === 'running') {
        alert('故障复位成功，系统保持运行状态');
      } else {
        alert('请先启动系统再执行故障复位');
      }
    },
    // 更新日期时间
    updateDateTime() {
      const now = new Date();
      const days = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];
      // 格式化日期：YYYY-MM-DD 星期X
      this.currentDate = `${now.getFullYear()}-${String(now.getMonth() + 1).padStart(2, '0')}-${String(now.getDate()).padStart(2, '0')} ${days[now.getDay()]}`;
      // 格式化时间：HH:MM:SS
      this.currentTime = `${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')}:${String(now.getSeconds()).padStart(2, '0')}`;
    },
    // 切换数据点标签的显示状态
    toggleDataLabels(pointName) {
      this.dataPointsVisibility[pointName] = !this.dataPointsVisibility[pointName];
    },
    
    // 更新实时数据，每个数据项独立变化，部分数据项可能保持不变，创造更自然的曲线形态
    updateRealTimeData() {
      // 只有在系统运行状态下才更新数据
      if (this.currentSystemState === 'running') {
        const runningData = this.systemData.running;
        
        // 基础变化幅度因子
        const baseChangeFactor = 0.3 + Math.random() * 0.5;
        
        // 更新powerGrid数据 - 电网电压在399.0-403.0V之间波动，80%概率变化
        if (Math.random() < 0.8) {
          const gridVoltageChangeUab = (Math.random() > 0.5 ? 1 : -1) * baseChangeFactor * 1.2;
          const newUab = Math.max(399.0, Math.min(403.0, parseFloat(runningData.powerGrid.Uab) + gridVoltageChangeUab));
          runningData.powerGrid.Uab = `${newUab.toFixed(1)}v`;
        }
        
        if (Math.random() < 0.8) {
          const gridVoltageChangeUbc = (Math.random() > 0.5 ? 1 : -1) * baseChangeFactor * 1.2;
          const newUbc = Math.max(399.0, Math.min(403.0, parseFloat(runningData.powerGrid.Ubc) + gridVoltageChangeUbc));
          runningData.powerGrid.Ubc = `${newUbc.toFixed(1)}v`;
        }
        
        if (Math.random() < 0.8) {
          const gridVoltageChangeUca = (Math.random() > 0.5 ? 1 : -1) * baseChangeFactor * 1.2;
          const newUca = Math.max(399.0, Math.min(403.0, parseFloat(runningData.powerGrid.Uca) + gridVoltageChangeUca));
          runningData.powerGrid.Uca = `${newUca.toFixed(1)}v`;
        }
        
        // 更新generator数据
        // 发电机电压：在400.0-402.0V之间波动，65%概率变化，变化相对平缓
        if (Math.random() < 0.65) {
          const generatorVoltageChange = (Math.random() > 0.6 ? 1 : -1) * baseChangeFactor * 0.6;
          const newGenUab = Math.max(400.0, Math.min(402.0, runningData.generator.UabValue + generatorVoltageChange));
          runningData.generator.UabValue = parseFloat(newGenUab.toFixed(1));
          runningData.generator.Uab = `${runningData.generator.UabValue.toFixed(1)}v`;
        }
        
        // 电流：在80.0-81.5A之间波动，85%概率变化，变化较为频繁
        if (Math.random() < 0.85) {
          const currentChange = (Math.random() > 0.5 ? 1 : -1) * baseChangeFactor * 3;
          const newCurrentA = Math.max(80.0, Math.min(81.5, runningData.generator.currentAValue + currentChange));
          runningData.generator.currentAValue = parseFloat(newCurrentA.toFixed(1));
          runningData.generator.currentA = `${runningData.generator.currentAValue.toFixed(1)}A`;
        }
        
        // 总有功功率：在55.0-56.5kW之间波动，85%概率变化，与电流有一定相关性但不完全同步
        if (Math.random() < 0.85) {
          const powerChange = (Math.random() > 0.52 ? 1 : -1) * baseChangeFactor * 5;
          const newPowerTotal = Math.max(55.0, Math.min(56.5, runningData.generator.powerTotalValue + powerChange));
          runningData.generator.powerTotalValue = parseFloat(newPowerTotal.toFixed(1));
          runningData.generator.powerTotal = `${runningData.generator.powerTotalValue.toFixed(1)}kw`;
        }
        
        // 无功功率：在16.0-17.5kvar之间波动，70%概率变化，独立变化
        if (Math.random() < 0.7) {
          const reactiveChange = (Math.random() > 0.48 ? 1 : -1) * baseChangeFactor * 1.5;
          const newReactiveTotal = Math.max(16.0, Math.min(17.5, parseFloat(runningData.generator.reactiveTotal) + reactiveChange));
          runningData.generator.reactiveTotal = `${newReactiveTotal.toFixed(1)}kvar`;
        }
        
        // 频率：在49.8-50.1Hz之间波动，60%概率变化，变化较小且稳定
        if (Math.random() < 0.6) {
          const frequencyChange = (Math.random() > 0.5 ? 1 : -1) * baseChangeFactor * 0.1;
          const newFrequency = Math.max(49.8, Math.min(50.1, runningData.generator.frequencyValue + frequencyChange));
          runningData.generator.frequencyValue = parseFloat(newFrequency.toFixed(1));
          runningData.generator.frequency = `${runningData.generator.frequencyValue.toFixed(1)}Hz`;
        }
        
        // 转速：在2995-3000 r/min之间波动，50%概率变化，变化缓慢且稳定
        if (Math.random() < 0.5) {
          const speedChange = (Math.random() > 0.6 ? 1 : -1) * Math.floor(baseChangeFactor * 5);
          const newSpeed = Math.max(2995, Math.min(3000, parseInt(runningData.generator.speed)) + speedChange);
          runningData.generator.speed = `${newSpeed.toFixed(1)} r/min`;
        }
        
        // 排气温度：在417.0-420.0℃之间波动，75%概率变化，与功率变化有一定相关性
        if (Math.random() < 0.75) {
          const exhaustTempChange = (Math.random() > 0.53 ? 1 : -1) * baseChangeFactor * 3;
          const newExhaustTemp = Math.max(417.0, Math.min(420.0, parseFloat(runningData.generator.exhaustTemp) + exhaustTempChange));
          runningData.generator.exhaustTemp = `${newExhaustTemp.toFixed(1)}℃`;
        }
        
        // 累计发电量：持续增长
        const newTotalPower = parseFloat(runningData.generator.totalPower) + 0.5 + Math.random() * 1.5;
        runningData.generator.totalPower = `${newTotalPower.toFixed(1)} kwh`;
        
        // 累计燃气量：持续增长
        const newTotalGas = parseFloat(runningData.generator.totalGas) + 0.3 + Math.random() * 0.7;
        runningData.generator.totalGas = `${newTotalGas.toFixed(1)} m³`;
        
        // 更新lithium数据
        // 冷水进水温度：在8.0-9.0℃之间波动，70%概率变化，独立变化
        if (Math.random() < 0.7) {
          const coldInTempChange = (Math.random() > 0.55 ? -1 : 1) * baseChangeFactor * 0.8; // 冷水温度变化方向特殊
          const newColdInTemp = Math.max(8.0, Math.min(9.0, runningData.lithium.coldInTempValue + coldInTempChange));
          runningData.lithium.coldInTempValue = parseFloat(newColdInTemp.toFixed(1));
          runningData.lithium.coldInTemp = `${runningData.lithium.coldInTempValue.toFixed(1)}℃`;
        }
        
        // 冷水出水温度：在12.0-13.5℃之间波动，65%概率变化，与进水温度有一定相关性
        if (Math.random() < 0.65) {
          const coldOutTempChange = (Math.random() > 0.55 ? -1 : 1) * baseChangeFactor * 0.8;
          const newColdOutTemp = Math.max(12.0, Math.min(13.5, parseFloat(runningData.lithium.coldOutTemp) + coldOutTempChange));
          runningData.lithium.coldOutTemp = `${newColdOutTemp.toFixed(1)}℃`;
        }
        
        // 烟气进口温度：在280-300℃之间波动，60%概率变化，独立变化
        if (Math.random() < 0.6) {
          const smokeInTempChange = (Math.random() > 0.52 ? 1 : -1) * baseChangeFactor * 3;
          const newsmokeInTemp = Math.max(280.0, Math.min(300.0, runningData.lithium.smokeInTempValue + smokeInTempChange));
          runningData.lithium.smokeInTempValue = parseFloat(newsmokeInTemp.toFixed(1));
          runningData.lithium.smokeInTemp = `${runningData.lithium.smokeInTempValue.toFixed(1)}℃`;
        }
        
        // 烟气出口温度：在60-70℃之间波动，55%概率变化，与进水温度相关但有延迟
        if (Math.random() < 0.55) {
          const smokeOutTempChange = (Math.random() > 0.52 ? 1 : -1) * baseChangeFactor * 3;
          const newsmokeOutTemp = Math.max(60.0, Math.min(70.0, parseFloat(runningData.lithium.smokeOutTemp) + smokeOutTempChange));
          runningData.lithium.smokeOutTemp = `${newsmokeOutTemp.toFixed(1)}℃`;
        }
        
        // 冷却水进水温度：在25.0-26.5℃之间波动，65%概率变化，独立变化
        if (Math.random() < 0.65) {
          const coolInTempChange = (Math.random() > 0.5 ? 1 : -1) * baseChangeFactor * 0.8;
          const newCoolInTemp = Math.max(28.5, Math.min(30.0, parseFloat(runningData.lithium.coolInTemp) + coolInTempChange));
          runningData.lithium.coolInTemp = `${newCoolInTemp.toFixed(1)}℃`;
        }
        
        // 冷却水出水温度：在28.5-30.0℃之间波动，60%概率变化，与进水温度相关
        if (Math.random() < 0.6) {
          const coolOutTempChange = (Math.random() > 0.5 ? 1 : -1) * baseChangeFactor * 0.8;
          const newCoolOutTemp = Math.max(25.0, Math.min(26.5, parseFloat(runningData.lithium.coolOutTemp) + coolOutTempChange));
          runningData.lithium.coolOutTemp = `${newCoolOutTemp.toFixed(1)}℃`;
        }
        
        // 蒸发器温度：在6.0-7.0℃之间波动，70%概率变化，独立变化
        if (Math.random() < 0.7) {
          const evaporatorTempChange = (Math.random() > 0.55 ? -1 : 1) * baseChangeFactor * 0.5;
          const newEvaporatorTemp = Math.max(6.0, Math.min(7.0, parseFloat(runningData.lithium.evaporatorTemp) + evaporatorTempChange));
          runningData.lithium.evaporatorTemp = `${newEvaporatorTemp.toFixed(1)}℃`;
        }
        
        // 蒸发器压力：在0.68-0.70Mpa之间波动，65%概率变化，独立变化
        if (Math.random() < 0.65) {
          const evaporatorPressChange = (Math.random() > 0.5 ? 1 : -1) * baseChangeFactor * 0.008;
          const newEvaporatorPress = Math.max(0.68, Math.min(0.70, parseFloat(runningData.lithium.evaporatorPress) + evaporatorPressChange));
          runningData.lithium.evaporatorPress = `${newEvaporatorPress.toFixed(2)}Mpa`;
        }
        
        // 为了更好地观察效果，添加控制台日志
        console.log('实时数据已更新，部分数据项保持不变以创建更自然的曲线形态');
      }
    },
    
    // 初始化3D场景和加载模型
    init3DScene() {
      // 如果容器不存在或模型路径未配置，则不执行3D初始化
      if (!this.$refs.modelContainer) {
        console.warn('3D模型容器不存在');
        return;
      }
      
      // 创建场景
      this.scene = new THREE.Scene();
      
      // 创建相机
      const containerWidth = this.$refs.modelContainer.offsetWidth;
      const containerHeight = this.$refs.modelContainer.offsetHeight;
      this.camera = new THREE.PerspectiveCamera(75, containerWidth / containerHeight, 0.1, 1000);
      
      // 创建渲染器
      this.renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      this.renderer.setSize(containerWidth, containerHeight);
      this.renderer.setClearColor(0x000000, 0); // 透明背景
      this.$refs.modelContainer.appendChild(this.renderer.domElement);
      
      // 添加环境光
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
      this.scene.add(ambientLight);
      
      // 添加方向光
      const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
      directionalLight.position.set(1, 1, 1);
      this.scene.add(directionalLight);
      
      // 加载GLB模型
      // 加载GLB模型
const loader = new GLTFLoader();
loader.load(
  '/equipment.glb',
  (gltf) => {
    // 关键修改：防止Vue代理Three.js对象
    this.model = markRaw(gltf.scene);
    
    // 设置模型初始旋转：沿y轴顺时针旋转90度，沿z轴顺时针旋转60度
    this.model.rotation.y = -Math.PI / 2;
    this.model.rotation.z = -Math.PI / 4;

    // 调整模型大小
    this.resizeModelToFitContainer();

    // 同理，如果 this.scene 是 reactive，也应该 markRaw 过
    this.scene.add(this.model);

    // 鼠标交互
    this.addMouseInteraction();
    
    // 添加设备信息按钮
    this.createDeviceButtons();
    console.log('设备按钮已创建');
    
    // 启动动画循环以更新按钮位置
    this.startAnimationLoop();
    console.log('动画循环已启动');

    console.log('3D模型加载成功，已适配容器大小，支持鼠标缩放和旋转');

    // 渲染一次
    this.renderer.render(this.scene, this.camera);
  },
  (xhr) => {
    let progress = 0;
    if (xhr.total && xhr.total > 0) {
      progress = (xhr.loaded / xhr.total * 100);
    } else if (xhr.loaded > 0) {
      progress = Math.min(99, xhr.loaded / 1000000 * 100);
    }
    progress = Math.max(0, Math.min(100, progress));
    this.modelLoadingProgress = progress;
    console.log(progress + '% 模型已加载');
  },
  (error) => {
    console.error('3D模型加载失败:', error);
  }
);

      
      // 添加窗口大小变化监听
      window.addEventListener('resize', this.handleResize);
    },
    
    // 调整模型大小以适配容器
    resizeModelToFitContainer() {
      if (!this.model || !this.$refs.modelContainer || !this.camera) return;
      
      const containerWidth = this.$refs.modelContainer.offsetWidth;
      const containerHeight = this.$refs.modelContainer.offsetHeight;
      
      // 计算模型的包围盒
      const box = new THREE.Box3().setFromObject(this.model);
      const size = box.getSize(new THREE.Vector3());
      const center = box.getCenter(new THREE.Vector3());
      
      // 确保模型居中
      this.model.position.x = -center.x;
      this.model.position.y = -center.y;
      this.model.position.z = -center.z;
      
     
      const scale = 8;
      this.model.scale.set(scale, scale, scale);
      
      // 重新计算相机位置，打破与scale的直接比例关系
      // 使用模型基础大小计算相机距离，并让相机距离增长速度小于scale增长速度
      const baseModelSize = Math.max(size.x, size.y, size.z);
      const fov = this.camera.fov * (Math.PI / 180);
      // 使用基础大小计算初始相机距离，然后与scale建立对数关系，避免比例增长
      const baseDistance = baseModelSize / (2 * Math.tan(fov / 2)) * 1.5;
      // 使用平方根关系，让相机距离增长速度慢于scale增长
      const cameraDistance = baseDistance * Math.sqrt(scale);
      
      // 设置相机位置
      this.camera.position.z = cameraDistance;
      this.camera.position.y = cameraDistance * 0.1; // 极低的俯视角度，确保更好地查看模型
      
      // 更新相机
      this.camera.aspect = containerWidth / containerHeight;
      this.camera.updateProjectionMatrix();
    },
    
    // 处理窗口大小变化
    handleResize() {
      if (!this.$refs.modelContainer || !this.camera || !this.renderer) return;
      
      const containerWidth = this.$refs.modelContainer.offsetWidth;
      const containerHeight = this.$refs.modelContainer.offsetHeight;
      
      this.camera.aspect = containerWidth / containerHeight;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(containerWidth, containerHeight);
      
      // 如果模型已加载，重新调整大小
      if (this.model) {
        this.resizeModelToFitContainer();
        this.renderer.render(this.scene, this.camera);
      }
    },
    
    // 添加鼠标交互功能
    addMouseInteraction() {
      if (!this.$refs.modelContainer) return;
      
      let isDragging = false;
      let isCtrlPressed = false;
      let previousMousePosition = { x: 0, y: 0 };
      
      // 创建方法引用以便在清理时移除监听器
      this.handleKeyDown = (event) => {
        if (event.key === 'Control' || event.key === 'Ctrl') {
          isCtrlPressed = true;
        }
      };
      
      this.handleKeyUp = (event) => {
        if (event.key === 'Control' || event.key === 'Ctrl') {
          isCtrlPressed = false;
        }
      };
      
      this.handleMouseUp = () => {
        isDragging = false;
      };
      
      // 监听Ctrl键按下和释放事件
      window.addEventListener('keydown', this.handleKeyDown);
      window.addEventListener('keyup', this.handleKeyUp);
      
      // 鼠标按下事件 - 开始拖拽
      this.$refs.modelContainer.addEventListener('mousedown', (event) => {
        isDragging = true;
        previousMousePosition = { x: event.clientX, y: event.clientY };
      });
      
      // 鼠标移动事件 - 处理旋转或平移
      this.$refs.modelContainer.addEventListener('mousemove', (event) => {
        if (!isDragging || !this.model) return;
        
        const deltaX = event.clientX - previousMousePosition.x;
        const deltaY = event.clientY - previousMousePosition.y;
        
        if (isCtrlPressed) {
            // 按住Ctrl键时，平移模型
            // 根据缩放比例调整平移速度
            const scale = this.model.scale.x;
            const moveSpeed = 0.005 * scale;
            
            this.model.position.x += deltaX * moveSpeed;
            this.model.position.y -= deltaY * moveSpeed;
          } else {
          // 普通拖拽时，旋转模型
          this.model.rotation.y += deltaX * 0.005;
          this.model.rotation.x += deltaY * 0.005;
          
          // 限制垂直旋转角度，避免过度旋转
          this.model.rotation.x = Math.max(-Math.PI / 2, Math.min(Math.PI / 2, this.model.rotation.x));
        }
        
        previousMousePosition = { x: event.clientX, y: event.clientY };
        this.renderer.render(this.scene, this.camera);
      });
      
      // 鼠标释放事件 - 结束拖拽
      window.addEventListener('mouseup', this.handleMouseUp);
      
      // 鼠标滚轮事件 - 处理缩放（仅在按住Ctrl键时生效）
      this.$refs.modelContainer.addEventListener('wheel', (event) => {
        event.preventDefault();
        
        // 只有在按住Ctrl键时才执行缩放
        if (!isCtrlPressed || !this.model) return;
        
        // 根据滚轮方向调整缩放比例，增大缩放步长
        const scaleFactor = event.deltaY > 0 ? 0.8 : 1.25;
        
        // 获取当前缩放值并计算新的缩放值
        const currentScale = this.model.scale.x;
        const newScale = Math.max(0.2, Math.min(100, currentScale * scaleFactor));
        
        // 应用缩放
        this.model.scale.set(newScale, newScale, newScale);
        
        this.renderer.render(this.scene, this.camera);
      });
    },
    
    // 创建设备信息按钮
    createDeviceButtons() {
      console.log('开始创建设备按钮...');
      
      if (!this.$refs.modelContainer) {
        console.error('modelContainer不存在');
        return;
      }
      
      console.log('modelContainer存在，容器大小:', {
        width: this.$refs.modelContainer.offsetWidth,
        height: this.$refs.modelContainer.offsetHeight
      });
      
      // 清除现有的按钮
      const existingButtons = document.querySelectorAll('.device-info-button');
      console.log(`清除${existingButtons.length}个现有按钮`);
      existingButtons.forEach(button => button.remove());
      
      // 创建设备按钮容器
      let buttonsContainer = document.getElementById('device-buttons-container');
      if (!buttonsContainer) {
        buttonsContainer = document.createElement('div');
        buttonsContainer.id = 'device-buttons-container';
        buttonsContainer.style.position = 'absolute';
        buttonsContainer.style.top = '0';
        buttonsContainer.style.left = '0';
        buttonsContainer.style.width = '100%';
        buttonsContainer.style.height = '100%';
        buttonsContainer.style.pointerEvents = 'none';
        buttonsContainer.style.zIndex = '10';
        this.$refs.modelContainer.appendChild(buttonsContainer);
        console.log('创建了按钮容器');
      }
      
      // 创建新按钮
      console.log(`准备创建${this.deviceButtons.length}个设备按钮`);
      this.deviceButtons.forEach(device => {
        if (!device.visible) {
          console.log(`跳过不可见设备: ${device.id}`);
          return;
        }
        
        console.log(`创建设备按钮: ${device.id} - ${device.name}, 位置:`, device.position);
        
        const button = document.createElement('div');
        button.className = 'device-info-button';
        button.dataset.deviceId = device.id;
        
        // 设置初始样式，确保可见
        button.style.position = 'absolute';
        button.style.width = '70px';
        button.style.height = '70px';
        button.style.backgroundColor = 'rgba(135, 206, 235, 0.8)';
        button.style.border = '2px solid #ffffff';
        button.style.borderRadius = '50%';
        button.style.display = 'flex';
        button.style.flexDirection = 'column';
        button.style.alignItems = 'center';
        button.style.justifyContent = 'center';
        button.style.cursor = 'pointer';
        button.style.boxShadow = '0 4px 12px rgba(0, 0, 0, 0.2)';
        button.style.zIndex = '100';
        button.style.pointerEvents = 'auto';
        button.style.transition = 'all 0.3s ease';
        
        button.innerHTML = `
          <div class="button-label" style="color: white; font-size: 14px; text-align: center; font-weight: bold;">${device.name}</div>
        `;
        
        // 添加点击事件
        button.addEventListener('click', (event) => {
          event.stopPropagation();
          console.log(`点击设备按钮: ${device.id}`);
          this.showDeviceDashboard(device);
        });
        
        // 添加到按钮容器
        buttonsContainer.appendChild(button);
        console.log(`设备按钮 ${device.id} 已添加到容器`);
        
        // 初始位置设置，先放在容器中心附近，确保可见
        button.style.left = '50%';
        button.style.top = '50%';
        button.style.transform = 'translate(-50%, -50%)';
      });
      
      console.log('设备按钮创建完成');
    },
    
    // 启动动画循环以更新按钮位置
    startAnimationLoop() {
      const animate = () => {
        this.animationId = requestAnimationFrame(animate);
        this.updateDeviceButtonPositions();
      };
      animate();
    },
    
    // 更新设备按钮位置
    updateDeviceButtonPositions() {
      if (!this.model || !this.camera || !this.renderer) {
        console.log('updateDeviceButtonPositions: 缺少必要的Three.js对象');
        return;
      }
      
      const container = this.$refs.modelContainer;
      if (!container) {
        console.log('updateDeviceButtonPositions: 容器不存在');
        return;
      }
      
      const rect = container.getBoundingClientRect();
      
      this.deviceButtons.forEach(device => {
        if (!device.visible) {
          console.log(`设备 ${device.id} 不可见`);
          return;
        }
        
        const button = document.querySelector(`.device-info-button[data-device-id="${device.id}"]`);
        if (!button) {
          console.log(`未找到设备 ${device.id} 的按钮元素`);
          return;
        }
        
        try {
          // 创建设备位置的向量
          const devicePosition = new THREE.Vector3(
            device.position.x,
            device.position.y,
            device.position.z
          );
          
          // 应用模型变换到设备位置
          const rotatedPosition = devicePosition.clone();
          rotatedPosition.applyMatrix4(this.model.matrixWorld);
          
          // 将3D位置转换为屏幕坐标
          const screenPosition = rotatedPosition.clone();
          screenPosition.project(this.camera);
          
          console.log(`设备 ${device.id} 3D位置:`, devicePosition);
          console.log(`设备 ${device.id} 旋转后位置:`, rotatedPosition);
          console.log(`设备 ${device.id} 屏幕位置:`, screenPosition);
          
          // 检查点是否在相机视野内
          if (screenPosition.z > 1) {
            console.log(`设备 ${device.id} 在相机视野外`);
            button.style.display = 'none';
            return;
          }
          
          // 计算屏幕坐标
          const x = (screenPosition.x * 0.5 + 0.5) * rect.width;
          const y = (-screenPosition.y * 0.5 + 0.5) * rect.height;
          
          // 设置按钮位置
          button.style.display = 'flex';
          button.style.left = `${x - 40}px`; // 按钮宽度的一半
          button.style.top = `${y - 40}px`; // 按钮高度的一半
          button.style.zIndex = '100'; // 确保按钮在顶层
          
          console.log(`设备 ${device.id} 按钮位置:`, { x: `${x - 40}px`, y: `${y - 40}px` });
        } catch (error) {
          console.error(`更新设备 ${device.id} 按钮位置时出错:`, error);
        }
      });
    },
    
    // 显示设备看板
    showDeviceDashboard(device) {
      this.selectedDevice = device;
      this.dashboardVisible = true;
    },
    
    // 关闭设备看板
    closeDeviceDashboard() {
      this.dashboardVisible = false;
      this.selectedDevice = null;
    }
  }
}
</script>
<style scoped>
/* 数字孪生数据样式 */
/* 旧的twin-data样式已移除，使用更具体的数据类型样式 */
/* 淡色调主题样式 */
.dashboard-container {
  width: 100%;
  background: linear-gradient(135deg, #f5f7fa 0%, #e4e8f0 100%);
  color: #2c3e50;
  font-family: 'Segoe UI', 'Microsoft YaHei', sans-serif;
  padding: 20px;
  box-sizing: border-box;
  position: relative;
  min-height: 100vh;
  overflow-y: auto;
}

/* 确保页面可以滚动 */
body {
  margin: 0;
  padding: 0;
  overflow-y: auto;
  background: #f5f7fa;
}

/* 设备信息按钮样式 */
.device-info-button {
  position: absolute;
  width: 80px;
  height: 80px;
  background: rgba(66, 133, 244, 0.9);
  border: 2px solid #ffffff;
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  transition: all 0.3s ease;
  z-index: 10;
  pointer-events: auto;
}

.device-info-button:hover {
  transform: scale(1.1);
  background: rgba(52, 119, 235, 0.95);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

.device-info-button .button-icon {
  width: 32px;
  height: 32px;
  color: #ffffff;
  margin-bottom: 4px;
}

.device-info-button .button-label {
  color: #ffffff;
  font-size: 12px;
  font-weight: 500;
  text-align: center;
  line-height: 1.2;
}

/* 设备看板模态框样式 */
.device-dashboard-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  /* backdrop-filter: blur(4px); */
}

.device-dashboard {
  background: linear-gradient(135deg, #ffffff 0%, #f8fafc 100%);
  border-radius: 16px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
  width: 90%;
  max-width: 800px;
  max-height: 80vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  border: 1px solid rgba(255, 255, 255, 0.5);
}

.dashboard-header {
  background: linear-gradient(135deg, #4285f4 0%, #3367d6 100%);
  color: #ffffff;
  padding: 20px 30px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
}

.dashboard-header h3 {
  margin: 0;
  font-size: 24px;
  font-weight: 600;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

.close-button {
  background: none;
  border: none;
  color: #ffffff;
  font-size: 32px;
  cursor: pointer;
  padding: 0;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: background 0.3s ease;
}

.close-button:hover {
  background: rgba(255, 255, 255, 0.2);
}


.close-button:hover {
  background: rgba(255, 255, 255, 0.2);
}

.dashboard-content {
  padding: 30px;
  overflow-y: auto;
  flex: 1;
}

.dashboard-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
}

.dashboard-item {
  background: linear-gradient(135deg, #ffffff 0%, #f1f5f9 100%);
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  border: 1px solid rgba(226, 232, 240, 0.5);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.dashboard-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
}

.item-label {
  font-size: 14px;
  color: #64748b;
  margin-bottom: 8px;
  font-weight: 500;
}

.item-value {
  font-size: 24px;
  font-weight: 700;
  color: #2c3e50;
  line-height: 1.2;
}

.item-value.normal {
  color: #10b981;
}

.item-value.abnormal {
  color: #ef4444;
}

/* 数据类型样式 */
.item-value.measured {
  color: #2c3e50; /* 黑色 - 实测值 */
}

.item-value.predicted {
  color: #4fc3f7; /* 蓝色 - 预测值 */
  font-weight: bold;
}

.item-value.digital-twin {
  color: #4caf50; /* 绿色 - 孪生数据 */
  font-weight: bold;
}

.dashboard-footer {
  background: linear-gradient(135deg, #f8fafc 0%, #e2e8f0 100%);
  padding: 15px 30px;
  border-top: 1px solid rgba(226, 232, 240, 0.5);
}

.update-time {
  color: #64748b;
  font-size: 14px;
  text-align: right;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .device-dashboard {
    width: 95%;
    max-height: 90vh;
  }
  
  .dashboard-header {
    padding: 15px 20px;
  }
  
  .dashboard-header h3 {
    font-size: 20px;
  }
  
  .dashboard-content {
    padding: 20px;
  }
  
  .dashboard-grid {
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 15px;
  }
  
  .dashboard-item {
    padding: 15px;
  }
  
  .item-value {
    font-size: 20px;
  }
}

/* 主标题样式 */
.dashboard-title {
  text-align: center;
  padding: 20px 0;
  margin-bottom: 20px;
  background: linear-gradient(135deg, rgba(66, 133, 244, 0.1), rgba(52, 119, 235, 0.1));
  border-bottom: 1px solid rgba(66, 133, 244, 0.3);
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.dashboard-title h1 {
  font-size: 28px;
  font-weight: 600;
  color: #4285f4;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
  margin: 0;
}

/* 时间显示样式 */
.datetime-display {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 15px 12px;
  background: linear-gradient(135deg, rgba(66, 133, 244, 0.15) 0%, rgba(52, 119, 235, 0.15) 100%);
  border: 1px solid rgba(66, 133, 244, 0.3);
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  /* backdrop-filter: blur(5px); */
  margin-bottom: 30px;
  width: 100%;
  max-width: 220px;
  position: relative;
  overflow: hidden;
}

.datetime-display::before {
  content: '';
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: repeating-linear-gradient(
    45deg,
    rgba(66, 133, 244, 0.05),
    rgba(66, 133, 244, 0.05) 10px,
    rgba(66, 133, 244, 0.03) 10px,
    rgba(66, 133, 244, 0.03) 20px
  );
  animation: gridMove 8s linear infinite;
  pointer-events: none;
}

@keyframes gridMove {
  0% { transform: translate(0, 0); }
  100% { transform: translate(20px, 20px); }
}

.date-line {
  font-size: 18px;
  color: #4285f4;
  font-weight: 600;
  font-family: 'Courier New', monospace;
  letter-spacing: 1px;
}

.time-line {
  font-size: 24px;
  color: #2c3e50;
  font-weight: bold;
  letter-spacing: 2px;
  font-family: 'Courier New', monospace;
}

/* 新布局样式 */
.top-section {
  display: flex;
  width: 100%;
  gap: 20px;
  margin-bottom: 20px;
  align-items: stretch;
}

.left-controls {
  width: 28%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 20px;
}

.central-image-container {
  width: 100%;
  height: 60vh;
  min-height: 400px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 10px;
  border: 1px solid rgba(66, 133, 244, 0.2);
  display: flex;
  align-items: center;
  padding-left: 0%;
  overflow: hidden;
  position: relative;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

.right-controls {
  width: 15%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 20px;
}

.left-buttons, .right-buttons {
  display: flex;
  flex-direction: column;
  gap: 20px;
  align-items: center;
}

.bottom-section {
  display: flex;
  width: 100%;
  gap: 20px;
  flex: 1;
  overflow-y: auto;
}

.bottom-left {
  width: 50%;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.bottom-right {
  width: 50%;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

/* 按钮样式 */
.dashboard-button {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 10px;
  padding: 12px 20px;
  background: rgba(255, 255, 255, 0.9);
  border: 1px solid #4285f4;
  border-radius: 8px;
  font-size: 14px;
  font-weight: normal;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: none;
  letter-spacing: normal;
  color: #2c3e50;
  min-width: 150px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
}

.dashboard-button:hover {
  background: rgba(66, 133, 244, 0.1);
  border-color: #3367d6;
  box-shadow: 0 4px 10px rgba(66, 133, 244, 0.2);
  transform: translateY(-2px);
}

.dashboard-button.active {
  background: linear-gradient(135deg, #4285f4 0%, #3367d6 100%);
  border-color: #3367d6;
  box-shadow: 0 4px 12px rgba(66, 133, 244, 0.3);
  color: white;
}

.dashboard-button:active {
  transform: scale(0.98);
}

.button-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  color: #4285f4;
  font-size: 16px;
}

.dashboard-button.primary {
  background: linear-gradient(135deg, rgba(52, 168, 83, 0.15), rgba(46, 125, 50, 0.15));
  border-color: #34a853;
  color: #2c3e50;
  box-shadow: 0 2px 6px rgba(52, 168, 83, 0.2);
}

.dashboard-button.primary:hover {
  background: linear-gradient(135deg, rgba(52, 168, 83, 0.25), rgba(46, 125, 50, 0.25));
  border-color: #2e7d32;
  box-shadow: 0 4px 10px rgba(52, 168, 83, 0.3);
}

.dashboard-button.secondary {
  background: linear-gradient(135deg, rgba(66, 133, 244, 0.1), rgba(52, 119, 235, 0.1));
  border-color: #4285f4;
  color: #2c3e50;
}

.dashboard-button.secondary:hover {
  background: linear-gradient(135deg, rgba(66, 133, 244, 0.2), rgba(52, 119, 235, 0.2));
  border-color: #3367d6;
}

/* 数据列表样式 */
.data-lists-container {
  display: flex;
  gap: 20px;
  flex: 1;
  width: 100%;
}

.data-list {
  flex: 1;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 10px;
  padding: 20px;
  border: 1px solid rgba(66, 133, 244, 0.2);
  width: 100%;
  overflow-y: auto;
  max-height: 600px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.list-title {
  font-size: 18px;
  margin-bottom: 20px;
  color: #4285f4;
  text-align: center;
  border-bottom: 2px solid rgba(66, 133, 244, 0.3);
  padding-bottom: 10px;
}

.data-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid rgba(0, 0, 0, 0.08);
}

.data-item:last-child {
  border-bottom: none;
}

.data-label {
  color: #5f6368;
  font-size: 14px;
}

.data-value {
  color: #2c3e50;
  font-size: 16px;
  font-weight: bold;
}

.data-value.normal {
  color: #34a853;
}

.data-value.abnormal {
  color: #ea4335;
}

/* 设备图片包装器 */
.equipment-image-wrapper {
  width: 100%;
  height: 100%;
  position: relative;
  display: flex;
  align-items: center;
  background-image: url('~@/../public/bg.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}

/* 数据点覆盖层 */
.data-points-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
  transform: translateX(0%);
}

/* 数据值显示样式 */
.data-value-display {
  position: absolute;
  transform: translate(-50%, -50%);
  pointer-events: auto;
  z-index: 10;
  white-space: nowrap;
  text-align: center;
}

/* 数据值样式 */
.data-value {
  font-size: 12px;
  font-weight: normal;
  color: #5f6368;
}

/* 数据值异常状态（停机） */
.data-value-display.alert {
  animation: alertPulse 1s infinite;
}

.data-value-display.alert .data-value {
  color: #ea4335;
}

/* 数据值正常状态（运行） */
.data-value-display.running {
  animation: runningPulse 1s infinite;
}

.data-value-display.running .data-value {
  color: #2c3e50;
}

@keyframes alertPulse {
  0% {
    text-shadow: 0 0 5px rgba(234, 67, 53, 0.3);
  }
  50% {
    text-shadow: 0 0 10px rgba(234, 67, 53, 0.5);
  }
  100% {
    text-shadow: 0 0 5px rgba(234, 67, 53, 0.3);
  }
}

@keyframes runningPulse {
  0% {
    text-shadow: 0 0 5px rgba(66, 133, 244, 0.3);
  }
  50% {
    text-shadow: 0 0 10px rgba(66, 133, 244, 0.5);
  }
  100% {
    text-shadow: 0 0 5px rgba(66, 133, 244, 0.3);
  }
}

.placeholder-image {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, rgba(66, 133, 244, 0.1), rgba(52, 119, 235, 0.1));
}

.placeholder-image span {
  font-size: 24px;
  color: rgba(66, 133, 244, 0.5);
  text-align: center;
  padding: 0 20px;
}

/* 圆形图表样式 */
.gauge-charts-container {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 15px;
  height: 25%;
}

.gauge-chart {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 10px;
}

.gauge-title {
  font-size: 14px;
  color: #5f6368;
  margin-bottom: 10px;
  text-align: center;
  white-space: nowrap;
}

.gauge-circle {
  position: relative;
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: conic-gradient(
    rgba(66, 133, 244, 0.2) 0%,
    rgba(66, 133, 244, 0.2) var(--progress, 0%),
    rgba(66, 133, 244, 0.8) var(--progress, 0%),
    rgba(66, 133, 244, 0.8) 100%
  );
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.gauge-circle::before {
  content: '';
  position: absolute;
  width: 70px;
  height: 70px;
  border-radius: 50%;
  background: #f5f7fa;
}

.gauge-value {
  position: relative;
  font-size: 14px;
  font-weight: bold;
  color: #2c3e50;
  text-align: center;
  white-space: nowrap;
}

/* 趋势图样式 */
.trend-charts-container {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.trend-chart-row {
  display: flex;
  gap: 20px;
  flex: 1;
}

.trend-chart {
  flex: 1;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 10px;
  border: 1px solid rgba(66, 133, 244, 0.2);
  padding: 15px;
  display: flex;
  flex-direction: column;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.trend-title {
  font-size: 14px;
  color: #5f6368;
  margin-bottom: 10px;
  text-align: center;
}

.trend-plot {
  flex: 1;
  background: rgba(255, 255, 255, 0.6);
  border-radius: 5px;
  padding: 10px;
}

/* 坐标轴样式优化 */
.axis line {
  stroke-linecap: round;
  stroke: #b0bec5;
}

.axis text {
  font-family: 'Segoe UI', sans-serif;
  letter-spacing: 0.5px;
  fill: #5f6368;
}

/* 数据点样式优化 */
.trend-plot circle {
  transition: transform 0.2s ease;
}

.trend-plot circle:hover {
  transform: scale(1.2);
}

.trend-plot text {
  font-weight: 500;
  text-shadow: 0 0 3px rgba(255, 255, 255, 0.8);
}

/* 响应式调整 */
@media (max-width: 1200px) {
  .gauge-charts-container {
    grid-template-columns: repeat(2, 1fr);
  }
  .trend-plot svg {
    viewBox: 0 0 400 160;
  }
  .axis text {
    font-size: 10px;
  }
  .trend-plot text {
    font-size: 9px;
  }
  .bottom-section {
    flex-direction: column;
  }
  .bottom-left, .bottom-right {
    width: 100%;
  }
}

@media (max-width: 768px) {
  .top-section {
    flex-direction: column;
  }
  .left-controls, .central-image-container, .right-controls {
    width: 100%;
  }
  .central-image-container {
    height: 40%;
    min-height: 250px;
  }
  .bottom-section {
    flex-direction: column;
  }
  .bottom-left, .bottom-right {
    width: 100%;
  }
  .gauge-charts-container {
    grid-template-columns: repeat(2, 1fr);
  }
  .trend-chart-row {
    flex-direction: column;
  }
  .trend-plot svg {
    viewBox: 0 0 380 150;
  }
  .axis text {
    font-size: 9px;
  }
  .trend-plot text {
    font-size: 8px;
  }
}
/* 在现有的CSS样式中添加或更新以下规则 */

.central-image-container {
  width: 100%;
  height: 60vh;
  min-height: 400px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  border: 1px solid rgba(66, 133, 244, 0.2);
  display: flex;
  align-items: center;
  padding-left: 0%;
  overflow: hidden;
  position: relative;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

.equipment-image-wrapper {
  width: 100%;
  height: 100%;
  position: relative;
  display: flex;
  align-items: center;
  border-radius: 10px;
  overflow: hidden;
}

/* 如果需要启用数据点显示，可以添加以下样式 */
.data-points-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
  transform: translateX(0%);
}

.data-value-display {
  position: absolute;
  transform: translate(-50%, -50%);
  pointer-events: auto;
  z-index: 10;
  white-space: nowrap;
  text-align: center;
  background: rgba(255, 255, 255, 0.9);
  border: 1px solid rgba(66, 133, 244, 0.3);
  border-radius: 6px;
  padding: 8px 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  /* backdrop-filter: blur(5px); */
}

.data-value {
  font-size: 12px;
  font-weight: 600;
  color: #2c3e50;
}

.data-value-display.alert .data-value {
  color: #ea4335;
}

.data-value-display.running .data-value {
  color: #34a853;
}

.placeholder-image {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, rgba(66, 133, 244, 0.1), rgba(52, 119, 235, 0.1));
  border-radius: 10px;
}

.placeholder-image span {
  font-size: 18px;
  color: rgba(66, 133, 244, 0.6);
  text-align: center;
  padding: 0 20px;
  font-weight: 500;
}
</style>