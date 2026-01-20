<template>
  <div class="diagnosis-container">
    <!-- 顶部导航栏 -->
    <div class="navbar">
      <button class="back-button" @click="goBack">
        <span class="back-icon">←</span>
        <span>返回仪表盘</span>
      </button>
      <h1 class="page-title">综合能源系统数字孪生运维管控平台</h1>
    </div>
    <!-- 主内容区域 -->
    <div class="diagnosis-content">
      <!-- 诊断状态卡片 -->
      <div class="diagnosis-status">
        <div class="status-card">
          <div class="status-title">系统状态</div>
          <div class="status-value" :class="systemStatus === '正常' ? 'normal' : 'abnormal'">
            {{ systemStatus }}
          </div>
          <div class="status-indicator" :class="systemStatus === '正常' ? 'normal' : 'abnormal'"></div>
        </div>
        <div class="status-card">
          <div class="status-title">活跃告警</div>
          <div class="status-value">{{ activeAlarms }} 个</div>
          <div class="status-change">{{ alarmTrend }}</div>
        </div>
        <div class="status-card">
          <div class="status-title">平均无故障时间</div>
          <div class="status-value">{{ mtbf }} 小时</div>
          <div class="status-change">+{{ mtbfChange }}%</div>
        </div>
        <div class="status-card">
          <div class="status-title">最近诊断时间</div>
          <div class="status-value">{{ lastDiagnosis }}</div>
          <div class="status-indicator update"></div>
        </div>
      </div>
      <!-- 设备概览图 -->
      <div class="equipment-overview">
        <h2 class="section-title">设备状态概览</h2>
        <div class="equipment-diagram">
          <div class="equipment-node" :class="pumpValvePipe.status">
            <div class="node-icon">🔧</div>
            <div class="node-name">泵阀及管网类设备</div>
            <div class="node-status">{{ pumpValvePipe.statusText }}</div>
          </div>
          <div class="equipment-arrow">→</div>
          <div class="equipment-node" :class="lithiumBromideUnit.status">
            <div class="node-icon">❄️</div>
            <div class="node-name">溴化锂制冷机组类设备</div>
            <div class="node-status">{{ lithiumBromideUnit.statusText }}</div>
          </div>
          <div class="equipment-arrow">→</div>
          <div class="equipment-node" :class="airConditionerTerminal.status">
            <div class="node-icon">🏠</div>
            <div class="node-name">空调末端类设备</div>
            <div class="node-status">{{ airConditionerTerminal.statusText }}</div>
          </div>
          <div class="equipment-arrow">→</div>
          <div class="equipment-node" :class="heatStorageExchanger.status">
            <div class="node-icon">🔥</div>
            <div class="node-name">储热与换热类设备</div>
            <div class="node-status">{{ heatStorageExchanger.statusText }}</div>
          </div>
          <div class="equipment-arrow">→</div>
          <div class="equipment-node" :class="hostUnit.status">
            <div class="node-icon">🖥️</div>
            <div class="node-name">主机类设备</div>
            <div class="node-status">{{ hostUnit.statusText }}</div>
          </div>
          <div class="equipment-arrow">→</div>
          <div class="equipment-node" :class="compressorUnit.status">
            <div class="node-icon">⚙️</div>
            <div class="node-name">压缩机类设备</div>
            <div class="node-status">{{ compressorUnit.statusText }}</div>
          </div>
          <div class="equipment-arrow">→</div>
          <div class="equipment-node" :class="unitSystemLevel.status">
            <div class="node-icon">🔗</div>
            <div class="node-name">机组系统级类设备</div>
            <div class="node-status">{{ unitSystemLevel.statusText }}</div>
          </div>
        </div>
      </div>
      <!-- 故障列表 -->
      <div class="fault-list">
        <h2 class="section-title">故障示意列表</h2>
        <div class="fault-table">
          <div class="table-header">
            <div class="header-cell">设备</div>
            <div class="header-cell">故障类型</div>
            <div class="header-cell">严重程度</div>
            <div class="header-cell">操作</div>
          </div>
          <div v-for="fault in faultData" :key="fault.id" class="table-row">
            <div class="table-cell">{{ fault.equipment }}</div>
            <div class="table-cell">{{ fault.type }}</div>
            <div class="table-cell"><span :class="'severity ' + fault.severity">{{ fault.severityText }}</span></div>
            <div class="table-cell">
              <button class="detail-button" @click="viewFaultDetail(fault)">详情</button>
            </div>
          </div>
        </div>
      </div>
      <!-- 诊断报告 -->
      <div class="diagnosis-report">
        <h2 class="section-title">诊断报告</h2>
        <div class="report-content">
          <div class="report-chart">
            <div class="chart-title">故障分布</div>
            <div class="chart-bars">
              <div class="chart-bar micro-turbine" style="height: 35%">
                <span>微燃机</span>
              </div>
              <div class="chart-bar lithium" style="height: 55%">
                <span>溴化锂机组</span>
              </div>
              <div class="chart-bar water-pump" style="height: 70%">
                <span>水泵/管道</span>
              </div>
              <div class="chart-bar air-conditioner" style="height: 40%">
                <span>空调末端</span>
              </div>
            </div>
          </div>
          <div class="maintenance-suggestions">
            <h3 class="suggestions-title">核心维护建议</h3>
            <ul class="suggestions-list">
              <li>每月清洗Y型过滤器（运行初期需加强频次），避免水系统堵塞</li>
              <li>系统最高点安装自动排气阀，每周手动辅助排气1次，排除管道空气</li>
              <li>每季度核对水泵参数，确保扬程/流量满足主机要求，避免流量不足</li>
              <li>每2个月清洗风机盘管回风滤网，同步对风盘手动排气</li>
              <li>每年对储水罐除垢+清洗生活热水侧板换，确保热水供应稳定</li>
              <li>主机安装基础每半年检查平整度，必要时补充减震垫，降低噪音振动</li>
              <li>高低压故障报警后，优先清洗换热器并排查冷媒泄漏，避免频繁停机</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'FaultDiagnosis',
  data() {
    return {
      systemStatus: '正常',
      activeAlarms: 2,
      alarmTrend: '-2',
      mtbf: 2980,
      mtbfChange: 3.8,
      lastDiagnosis: '2025-12-23 09:45',
      
      // 设备状态（7类设备，保留2个轻微故障，其余正常）
      pumpValvePipe: {
        status: 'warning',
        statusText: '管道堵塞'
      },
      lithiumBromideUnit: {
        status: 'normal',
        statusText: '正常'
      },
      airConditionerTerminal: {
        status: 'normal',
        statusText: '正常'
      },
      heatStorageExchanger: {
        status: 'normal',
        statusText: '正常'
      },
      hostUnit: {
        status: 'normal',
        statusText: '正常'
      },
      compressorUnit: {
        status: 'normal',
        statusText: '正常'
      },
      unitSystemLevel: {
        status: 'normal',
        statusText: '正常'
      },
      
      // 故障数据（覆盖7类设备）
      faultData: [
        {
          id: 1,
          equipment: '泵阀及管网类设备',
          type: '管道堵塞',
          severity: 'medium',
          severityText: '中等',
          solution: {
            reason: '管道内杂质堆积、阀门开度不足、介质粘度异常、管道老化变形',
            measure: '1.定期清洗管道滤网和Y型过滤器；2.检查阀门开度并校准执行器；3.优化介质参数，定期检测介质粘度；4.更换老化变形管道段'
          }
        },
        {
          id: 2,
          equipment: '溴化锂制冷机组类设备',
          type: '溴液结晶',
          severity: 'high',
          severityText: '严重',
          solution: {
            reason: '溶液浓度过高、加热温度异常、冷媒水温度过低、机组真空度不足',
            measure: '1.稀释溴化锂溶液至标准浓度；2.校准加热系统温度传感器；3.调整冷媒水温度至合理范围；4.检查机组密封并抽真空'
          }
        },
        {
          id: 3,
          equipment: '空调末端类设备',
          type: '风阀执行器卡滞',
          severity: 'low',
          severityText: '轻微',
          solution: {
            reason: '执行器机械卡涩、电机故障、控制信号异常、连杆机构变形',
            measure: '1.拆解清洗执行器机械结构；2.检测电机绕组和供电；3.校准控制信号参数；4.校正或更换变形连杆'
          }
        },
        {
          id: 4,
          equipment: '储热与换热类设备',
          type: '换热器结垢堵塞',
          severity: 'medium',
          severityText: '中等',
          solution: {
            reason: '水质硬度高、换热介质杂质多、流速过低、温度差过大',
            measure: '1.采用化学清洗或物理清洗去除结垢；2.加装水质软化装置；3.优化换热介质流速；4.调整换热温差至设计范围'
          }
        },
        {
          id: 5,
          equipment: '主机类设备',
          type: '轴承故障',
          severity: 'high',
          severityText: '严重',
          solution: {
            reason: '润滑不足、轴承磨损、安装偏差、振动过大、负载异常',
            measure: '1.补充或更换专用润滑油；2.更换磨损轴承；3.重新校准安装精度；4.加装减震装置并优化负载分配'
          }
        },
        {
          id: 6,
          equipment: '压缩机类设备',
          type: '吸排气压力异常',
          severity: 'medium',
          severityText: '中等',
          solution: {
            reason: '制冷剂泄漏、滤网堵塞、膨胀阀故障、环境温度异常',
            measure: '1.查漏并补充制冷剂；2.清洗吸排气滤网；3.检修或更换膨胀阀；4.优化机房环境温度，加装通风散热装置'
          }
        },
        {
          id: 7,
          equipment: '机组系统级类设备',
          type: '控制系统失效',
          severity: 'high',
          severityText: '严重',
          solution: {
            reason: 'PLC程序故障、传感器信号丢失、通讯链路中断、电源模块故障',
            measure: '1.恢复PLC备份程序并调试；2.检查传感器供电和接线；3.修复通讯链路故障点；4.更换故障电源模块'
          }
        }
      ]
    }
  },
  methods: {
    goBack() {
      this.$router.push('/')
    },
    viewFaultDetail(fault) {
      alert(`
        【故障详情】
        设备：${fault.equipment}
        故障类型：${fault.type}
        严重程度：${fault.severityText}
        \n【可能原因】
        ${fault.solution.reason}
        \n【优化措施】
        ${fault.solution.measure}
      `)
    }
  }
}
</script>

<style scoped>
.diagnosis-container {
  width: 100%;
  height: 100vh;
  background: linear-gradient(135deg, #f5f7fa 0%, #e4e8f0 100%);
  color: #2c3e50;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  font-family: 'Segoe UI', 'Microsoft YaHei', sans-serif;
}

.navbar {
  display: flex;
  align-items: center;
  padding: 15px 25px;
  background: rgba(255, 255, 255, 0.9);
  border-bottom: 1px solid rgba(66, 133, 244, 0.3);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.back-button {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: rgba(66, 133, 244, 0.1);
  border: 1px solid rgba(66, 133, 244, 0.3);
  border-radius: 6px;
  color: #2c3e50;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 14px;
}

.back-button:hover {
  background: rgba(66, 133, 244, 0.2);
  box-shadow: 0 2px 8px rgba(66, 133, 244, 0.2);
  transform: translateY(-1px);
}

.page-title {
  flex: 1;
  text-align: center;
  font-size: 24px;
  font-weight: 600;
  color: #4285f4;
  margin: 0;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

.diagnosis-content {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.section-title {
  font-size: 20px;
  color: #4285f4;
  margin-bottom: 15px;
  border-bottom: 2px solid rgba(66, 133, 244, 0.3);
  padding-bottom: 10px;
  font-weight: 600;
}

.diagnosis-status {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
}

.status-card {
  background: rgba(255, 255, 255, 0.9);
  border: 1px solid rgba(66, 133, 244, 0.2);
  border-radius: 10px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s ease;
}

.status-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.1);
}

.status-title {
  font-size: 14px;
  color: #5f6368;
  font-weight: 500;
}

.status-value {
  font-size: 24px;
  font-weight: bold;
  color: #2c3e50;
}

.status-value.normal {
  color: #34a853;
}

.status-value.abnormal {
  color: #ea4335;
}

.status-indicator {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  align-self: flex-end;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.2);
}

.status-indicator.normal {
  background: #34a853;
}

.status-indicator.abnormal {
  background: #ea4335;
}

.status-indicator.update {
  background: #4285f4;
}

.status-change {
  font-size: 12px;
  align-self: flex-end;
  font-weight: 500;
  color: #34a853;
}

.equipment-overview {
  background: rgba(255, 255, 255, 0.9);
  border: 1px solid rgba(66, 133, 244, 0.2);
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.equipment-diagram {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
  flex-wrap: wrap;
}

.equipment-node {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 15px 20px;
  background: rgba(255, 255, 255, 0.8);
  border: 2px solid rgba(66, 133, 244, 0.3);
  border-radius: 10px;
  transition: all 0.3s ease;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
}

.equipment-node:hover {
  transform: translateY(-3px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.equipment-node.normal {
  border-color: #34a853;
  background: rgba(52, 168, 83, 0.1);
}

.equipment-node.warning {
  border-color: #fbbc05;
  background: rgba(251, 188, 5, 0.1);
}

.equipment-node.error {
  border-color: #ea4335;
  background: rgba(234, 67, 53, 0.1);
}

.node-icon {
  font-size: 32px;
  margin-bottom: 8px;
}

.node-name {
  font-size: 14px;
  font-weight: bold;
  color: #2c3e50;
  margin-bottom: 5px;
}

.node-status {
  font-size: 12px;
  color: #5f6368;
  font-weight: 500;
}

.equipment-arrow {
  font-size: 20px;
  color: #4285f4;
  font-weight: bold;
}

.fault-list {
  background: rgba(255, 255, 255, 0.9);
  border: 1px solid rgba(66, 133, 244, 0.2);
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.fault-table {
  background: rgba(248, 249, 250, 0.8);
  border-radius: 8px;
  overflow: hidden;
  border: 1px solid rgba(66, 133, 244, 0.1);
}

.table-header {
  display: grid;
  grid-template-columns: 1fr 1.8fr 1fr 0.8fr;
  background: rgba(66, 133, 244, 0.1);
  padding: 15px;
  font-weight: bold;
  color: #4285f4;
  font-size: 14px;
}

.table-row {
  display: grid;
  grid-template-columns: 1fr 1.8fr 1fr 0.8fr;
  border-bottom: 1px solid rgba(66, 133, 244, 0.1);
  transition: background 0.3s ease;
}

.table-row:hover {
  background: rgba(66, 133, 244, 0.05);
}

.table-cell {
  padding: 12px;
  color: #5f6368;
  font-size: 13px;
  font-weight: 500;
  display: flex;
  align-items: center;
}

.severity {
  padding: 4px 10px;
  border-radius: 15px;
  font-size: 12px;
  font-weight: bold;
  border: 1px solid;
}

.severity.low {
  background: rgba(52, 168, 83, 0.1);
  color: #34a853;
  border-color: #34a853;
}

.severity.medium {
  background: rgba(251, 188, 5, 0.1);
  color: #fbbc05;
  border-color: #fbbc05;
}

.severity.high {
  background: rgba(234, 67, 53, 0.1);
  color: #ea4335;
  border-color: #ea4335;
}

.status {
  padding: 4px 10px;
  border-radius: 15px;
  font-size: 12px;
  font-weight: bold;
  border: 1px solid;
}

.status.pending {
  background: rgba(251, 188, 5, 0.1);
  color: #fbbc05;
  border-color: #fbbc05;
}

.status.processing {
  background: rgba(66, 133, 244, 0.1);
  color: #4285f4;
  border-color: #4285f4;
}

.status.resolved {
  background: rgba(52, 168, 83, 0.1);
  color: #34a853;
  border-color: #34a853;
}

.detail-button {
  padding: 6px 12px;
  background: rgba(66, 133, 244, 0.1);
  border: 1px solid rgba(66, 133, 244, 0.3);
  border-radius: 4px;
  color: #4285f4;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 13px;
  font-weight: 500;
}

.detail-button:hover {
  background: rgba(66, 133, 244, 0.2);
  box-shadow: 0 2px 6px rgba(66, 133, 244, 0.2);
  transform: translateY(-1px);
}

.diagnosis-report {
  background: rgba(255, 255, 255, 0.9);
  border: 1px solid rgba(66, 133, 244, 0.2);
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.report-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.report-chart {
  background: rgba(248, 249, 250, 0.8);
  border-radius: 8px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  border: 1px solid rgba(66, 133, 244, 0.1);
}

.chart-title {
  font-size: 16px;
  color: #4285f4;
  text-align: center;
  font-weight: 600;
}

.chart-bars {
  display: flex;
  align-items: flex-end;
  justify-content: space-around;
  height: 200px;
  gap: 15px;
}

.chart-bar {
  flex: 1;
  border-radius: 6px 6px 0 0;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: center;
  padding-bottom: 10px;
  transition: all 0.3s ease;
  position: relative;
  border: 1px solid;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.chart-bar:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.chart-bar.micro-turbine {
  border-color: #fbbc05;
  background: linear-gradient(135deg, rgba(251, 188, 5, 0.2), rgba(251, 188, 5, 0.3));
}

.chart-bar.lithium {
  border-color: #ea4335;
  background: linear-gradient(135deg, rgba(234, 67, 53, 0.2), rgba(234, 67, 53, 0.3));
}

.chart-bar.water-pump {
  border-color: #ea4335;
  background: linear-gradient(135deg, rgba(234, 67, 53, 0.3), rgba(234, 67, 53, 0.4));
}

.chart-bar.air-conditioner {
  border-color: #fbbc05;
  background: linear-gradient(135deg, rgba(251, 188, 5, 0.3), rgba(251, 188, 5, 0.4));
}

.chart-bar span {
  font-size: 12px;
  color: #2c3e50;
  font-weight: bold;
}

.maintenance-suggestions {
  background: rgba(248, 249, 250, 0.8);
  border-radius: 8px;
  padding: 20px;
  border: 1px solid rgba(66, 133, 244, 0.1);
}

.suggestions-title {
  font-size: 16px;
  color: #4285f4;
  margin-bottom: 15px;
  font-weight: 600;
}

.suggestions-list {
  list-style-type: none;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.suggestions-list li {
  font-size: 13px;
  color: #5f6368;
  padding-left: 20px;
  position: relative;
  line-height: 1.4;
  font-weight: 500;
}

.suggestions-list li::before {
  content: '•';
  color: #4285f4;
  position: absolute;
  left: 0;
  font-weight: bold;
  font-size: 16px;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .diagnosis-status {
    grid-template-columns: repeat(2, 1fr);
  }
  .equipment-diagram {
    flex-direction: column;
  }
  .equipment-arrow {
    transform: rotate(90deg);
  }
  .table-header,
  .table-row {
    grid-template-columns: 1fr;
  }
  .report-content {
    grid-template-columns: 1fr;
  }
}
</style>