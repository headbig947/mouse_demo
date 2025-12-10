<template>
  <div class="container">
    <h1>ATU_MOUSE 通信协议调试器</h1>

    <!-- 设备信息 -->
    <el-card class="info-card">
      <div class="info-row">
        <span class="info-label">设备名称:</span>
        <span class="info-value">ATU_MOUSE</span>
      </div>
      <div class="info-row">
        <span class="info-label">VID:</span>
        <span class="info-value">4242</span>
      </div>
      <div class="info-row">
        <span class="info-label">PID:</span>
        <span class="info-value">0009</span>
      </div>
      <div class="info-row">
        <span class="info-label">Report ID:</span>
        <span class="info-value">09</span>
      </div>
    </el-card>

    <!-- 功能区域 -->
    <el-row :gutter="20">
      <!-- DPI设置 -->
      <el-col :span="12">
        <el-card class="function-card">
          <template #header>
            <span class="card-title">DPI 设置</span>
          </template>
          <div style="margin-bottom: 15px;">
            <el-select v-model="dpiValue" placeholder="选择DPI值" style="width: 150px; margin-right: 10px;">
              <el-option label="800" value="0320" />
              <el-option label="1600" value="0640" />
              <el-option label="3200" value="0C80" />
              <el-option label="4000" value="0FA0" />
              <el-option label="6000" value="1770" />
              <el-option label="8000" value="1F40" />
            </el-select>
            <el-button type="primary" @click="setDpi">设置DPI</el-button>
            <el-button @click="queryDpi">查询DPI</el-button>
          </div>
          <el-input v-model="customDpi" placeholder="自定义DPI (十六进制)" style="width: 200px; margin-right: 10px;" />
          <el-button @click="setCustomDpi">设置自定义DPI</el-button>
        </el-card>
      </el-col>

      <!-- 按键设置 -->
      <el-col :span="12">
        <el-card class="function-card">
          <template #header>
            <span class="card-title">按键设置</span>
          </template>
          <div style="margin-bottom: 10px;">
            <el-select v-model="keyId" placeholder="选择按键" style="width: 100px; margin-right: 10px;">
              <el-option label="左键" value="00" />
              <el-option label="右键" value="01" />
              <el-option label="中键" value="02" />
              <el-option label="前进键" value="03" />
              <el-option label="后退键" value="04" />
              <el-option label="DPI键" value="05" />
            </el-select>
            <el-select v-model="keyType" placeholder="按键类型" style="width: 120px; margin-right: 10px;">
              <el-option label="默认按键" value="00" />
              <el-option label="系统按键" value="01" />
              <el-option label="板载配置" value="02" />
              <el-option label="DPI按键" value="03" />
              <el-option label="鼠标滚轮" value="04" />
              <el-option label="多媒体" value="05" />
              <el-option label="键盘按键" value="06" />
              <el-option label="F区功能键" value="07" />
              <el-option label="数字小键盘" value="08" />
              <el-option label="控制键" value="09" />
              <el-option label="火力键" value="0A" />
              <el-option label="组合键" value="0B" />
              <el-option label="宏" value="0C" />
            </el-select>
            <el-button @click="queryKey">查询</el-button>
          </div>
          <div v-if="keyType === '01'" style="margin-bottom: 10px;">
            <el-select v-model="systemKeyIndex" placeholder="选择功能" style="width: 150px;">
              <el-option label="禁用" value="00" />
              <el-option label="左键" value="01" />
              <el-option label="右键" value="02" />
              <el-option label="中键" value="03" />
              <el-option label="后退" value="04" />
              <el-option label="前进" value="05" />
            </el-select>
            <el-button type="primary" @click="setKey" style="margin-left: 10px;">设置</el-button>
          </div>
          <div v-if="keyType === '05'" style="margin-bottom: 10px;">
            <el-select v-model="mediaKeyIndex" placeholder="多媒体键" style="width: 200px;">
              <el-option label="播放器" value="00" />
              <el-option label="停止播放" value="01" />
              <el-option label="播放/暂停" value="02" />
              <el-option label="上一首" value="03" />
              <el-option label="下一首" value="04" />
              <el-option label="静音" value="05" />
              <el-option label="音量+" value="06" />
              <el-option label="音量-" value="07" />
            </el-select>
            <el-button type="primary" @click="setMediaKey" style="margin-left: 10px;">设置</el-button>
          </div>
          <div v-if="keyType === '0B'" style="margin-bottom: 10px;">
            <el-input v-model="comboKeys" placeholder="组合键值 (十六进制)" style="width: 200px; margin-right: 10px;" />
            <el-button type="primary" @click="setComboKey">设置组合键</el-button>
          </div>
          <div v-if="keyType === '0A'" style="margin-bottom: 10px;">
            <el-input v-model="fireInterval" placeholder="间隔(ms)" style="width: 100px; margin-right: 10px;" />
            <el-input v-model="fireCount" placeholder="次数" style="width: 100px; margin-right: 10px;" />
            <el-button type="primary" @click="setFireKey">设置火力键</el-button>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <!-- 板载配置 -->
    <el-row :gutter="20" style="margin-top: 20px;">
      <el-col :span="12">
        <el-card class="function-card">
          <template #header>
            <span class="card-title">板载配置</span>
          </template>
          <el-select v-model="profileId" placeholder="配置编号" style="width: 150px; margin-right: 10px;">
            <el-option label="配置 0" value="00" />
            <el-option label="配置 1" value="01" />
            <el-option label="配置 2" value="02" />
            <el-option label="配置 3" value="03" />
          </el-select>
          <el-button type="primary" @click="setProfile">切换配置</el-button>
          <el-button @click="queryProfile">查询配置</el-button>
        </el-card>
      </el-col>

      <!-- 恢复默认设置 -->
      <el-col :span="12">
        <el-card class="function-card">
          <template #header>
            <span class="card-title">恢复默认设置</span>
          </template>
          <el-select v-model="resetType" placeholder="恢复类型" style="width: 150px; margin-right: 10px;">
            <el-option label="所有设置" value="00" />
            <el-option label="仅按键" value="01" />
          </el-select>
          <el-button type="warning" @click="resetSettings">恢复默认</el-button>
        </el-card>
      </el-col>
    </el-row>

    <!-- 系统信息 -->
    <el-row :gutter="20" style="margin-top: 20px;">
      <el-col :span="12">
        <el-card class="function-card">
          <template #header>
            <span class="card-title">系统信息</span>
          </template>
          <el-button @click="queryVersion('00')" style="margin-right: 10px;">查询鼠标版本</el-button>
          <el-button @click="queryVersion('01')">查询接收器版本</el-button>
          <el-button @click="queryMode" style="margin-left: 10px;">查询工作模式</el-button>
        </el-card>
      </el-col>

      <!-- 批量操作 -->
      <el-col :span="12">
        <el-card class="function-card">
          <template #header>
            <span class="card-title">批量操作</span>
          </template>
          <el-button type="success" @click="getAllSettings">获取所有设置</el-button>
          <el-button type="danger" @click="clearAll">清空显示</el-button>
        </el-card>
      </el-col>
    </el-row>

    <!-- 数据包显示区域 -->
    <el-card class="packet-card">
      <template #header>
        <span class="card-title">通信数据包</span>
      </template>
      <el-table :data="packetList" style="width: 100%" max-height="400">
        <el-table-column prop="direction" label="方向" width="80">
          <template #default="{ row }">
            <el-tag :type="row.direction === '发送' ? 'primary' : 'success'">
              {{ row.direction }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="time" label="时间" width="100" />
        <el-table-column prop="function" label="功能" width="120" />
        <el-table-column prop="data" label="数据包 (十六进制)">
          <template #default="{ row }">
            <div class="packet-data">
              <span
                  v-for="(byte, index) in formatPacket(row.data)"
                  :key="index"
                  :class="getByteClass(index)"
              >
                {{ byte }}
              </span>
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="crc" label="CRC" width="80" />
      </el-table>
    </el-card>

    <!-- 详情对话框 -->
    <el-dialog v-model="detailDialogVisible" title="数据包详情" width="600px">
      <div v-if="selectedPacket">
        <h3>数据包结构解析</h3>
        <div class="packet-detail">
          <div class="detail-row">
            <span class="detail-label">Report ID:</span>
            <span class="detail-value">09</span>
            <span class="detail-desc">固定值</span>
          </div>
          <div class="detail-row">
            <span class="detail-label">读写控制:</span>
            <span class="detail-value">{{ selectedPacket.data.slice(2, 4) }}</span>
            <span class="detail-desc">{{ selectedPacket.data[2] === '8' ? '读取' : '写入' }}</span>
          </div>
          <div class="detail-row">
            <span class="detail-label">命令类型:</span>
            <span class="detail-value">{{ selectedPacket.data.slice(4, 6) }}</span>
            <span class="detail-desc">{{ getCommandDesc(selectedPacket.data.slice(4, 6)) }}</span>
          </div>
          <div class="detail-row">
            <span class="detail-label">数据长度:</span>
            <span class="detail-value">{{ selectedPacket.data.slice(6, 8) }}</span>
            <span class="detail-desc">{{ parseInt(selectedPacket.data.slice(6, 8), 16) }} 字节</span>
          </div>
          <div class="detail-row">
            <span class="detail-label">数据CRC:</span>
            <span class="detail-value">{{ selectedPacket.data.slice(-2) }}</span>
            <span class="detail-desc">有效数据异或值</span>
          </div>
        </div>
        <h3>完整数据</h3>
        <el-input
            v-model="selectedPacket.rawData"
            type="textarea"
            rows="3"
            readonly
        />
      </div>
    </el-dialog>

    <!-- 宏编辑器对话框 -->
    <el-dialog v-model="macroDialogVisible" title="宏设置" width="700px">
      <div v-if="currentMacro">
        <div style="margin-bottom: 15px;">
          <el-select v-model="currentMacro.repeatType" placeholder="循环类型" style="width: 150px; margin-right: 10px;">
            <el-option label="按住循环" value="F0" />
            <el-option label="循环至此键按下" value="F1" />
            <el-option label="循环至任何键按下" value="F2" />
            <el-option label="按次数循环" value="00" />
          </el-select>
          <el-input v-model="currentMacro.repeatCount" placeholder="循环次数" style="width: 100px;" v-if="currentMacro.repeatType === '00'" />
        </div>
        <div class="macro-steps">
          <h4>宏步骤 ({{ currentMacro.steps.length }})</h4>
          <div v-for="(step, index) in currentMacro.steps" :key="index" class="macro-step">
            <el-select v-model="step.type" placeholder="类型" style="width: 120px; margin-right: 10px;">
              <el-option label="按键放开" value="00" />
              <el-option label="鼠标键按下" value="01" />
              <el-option label="键盘键按下" value="02" />
            </el-select>
            <el-input v-model="step.value" placeholder="键值" style="width: 100px; margin-right: 10px;" />
            <el-input v-model="step.duration" placeholder="持续时间(ms)" style="width: 120px; margin-right: 10px;" />
            <el-button type="danger" size="small" @click="removeMacroStep(index)">删除</el-button>
          </div>
          <el-button type="primary" size="small" @click="addMacroStep" style="margin-top: 10px;">添加步骤</el-button>
        </div>
        <div style="margin-top: 20px; text-align: center;">
          <el-button type="success" @click="saveMacro">保存宏设置</el-button>
        </div>
      </div>
    </el-dialog>
  </div>
</template>

<script setup>
import {reactive, ref} from 'vue'
import {ElMessage} from 'element-plus'

// 数据定义
const packetList = ref([])
const dpiValue = ref('0320')
const customDpi = ref('')
const keyId = ref('00')
const keyType = ref('01')
const systemKeyIndex = ref('01')
const mediaKeyIndex = ref('00')
const comboKeys = ref('')
const fireInterval = ref('08')
const fireCount = ref('03')
const profileId = ref('00')
const resetType = ref('00')
const detailDialogVisible = ref(false)
const macroDialogVisible = ref(false)
const selectedPacket = ref(null)

// 宏数据
const currentMacro = reactive({
  repeatType: 'F0',
  repeatCount: '01',
  steps: []
})

// DPI预设值
const dpiPresets = {
  '0320': 800,
  '0640': 1600,
  '0C80': 3200,
  '0FA0': 4000,
  '1770': 6000,
  '1F40': 8000
}

// CRC计算函数
function calculateCRC(data) {
  const sum = data.reduce((acc, val) => acc + parseInt(val, 16), 0)
  return ((0 - sum) & 0xFF).toString(16).toUpperCase().padStart(2, '0')
}

// 数据CRC计算
function calculateDataCRC(data) {
  if (data.length === 0) return '00'
  if (data.length === 1) return data[0].toUpperCase().padStart(2, '0')
  return data.reduce((a, b) => {
    const xor = parseInt(a, 16) ^ parseInt(b, 16)
    return xor.toString(16).toUpperCase().padStart(2, '0')
  })
}

// 构建数据包
function buildPacket(rwControl, commandType, dataLength, dataArray) {
  const packet = ['09', rwControl, commandType, dataLength]

  // 添加数据
  for (let i = 0; i < 12; i++) {
    packet.push(dataArray[i] || '00')
  }

  // 计算数据CRC
  const dataCrcIndex = 4 + parseInt(dataLength, 16)
  packet[dataCrcIndex] = calculateDataCRC(dataArray.slice(0, parseInt(dataLength, 16)))

  // 计算主CRC
  packet[16] = calculateCRC(packet.slice(0, 16))

  return packet
}

// 添加数据包到列表
function addPacket(direction, func, packet) {
  const now = new Date()
  const timeStr = `${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}:${now.getSeconds().toString().padStart(2, '0')}`

  packetList.value.unshift({
    direction,
    time: timeStr,
    function: func,
    data: packet.join(' '),
    crc: packet[16],
    rawData: packet.join(' ')
  })
}

// DPI设置
function setDpi() {
  const packet = buildPacket('81', '90', '02', [dpiValue.value.slice(0, 2), dpiValue.value.slice(2, 4)])
  addPacket('发送', `设置DPI ${dpiPresets[dpiValue.value] || '未知'}`, packet)
  // 模拟返回
  setTimeout(() => addPacket('接收', 'DPI设置成功', packet), 100)
  ElMessage.success(`DPI已设置为 ${dpiPresets[dpiValue.value] || '未知'}`)
}

function setCustomDpi() {
  if (!customDpi.value || customDpi.value.length !== 4) {
    ElMessage.error('请输入4位十六进制DPI值')
    return
  }
  const packet = buildPacket('81', '90', '02', [customDpi.value.slice(0, 2), customDpi.value.slice(2, 4)])
  addPacket('发送', `设置自定义DPI`, packet)
  setTimeout(() => addPacket('接收', '自定义DPI设置成功', packet), 100)
  ElMessage.success('自定义DPI设置成功')
}

function queryDpi() {
  const packet = buildPacket('80', '90', '00', [])
  addPacket('发送', '查询DPI', packet)
  // 模拟返回
  const response = buildPacket('81', '90', '02', [dpiValue.value.slice(0, 2), dpiValue.value.slice(2, 4)])
  setTimeout(() => addPacket('接收', 'DPI查询结果', response), 100)
}

// 按键设置
function queryKey() {
  const packet = buildPacket('80', '91', '01', [keyId.value, '00'])
  addPacket('发送', `查询按键${getKeyName(keyId.value)}`, packet)
  // 模拟返回
  const response = buildPacket('81', '91', '02', [keyId.value, keyType.value, '00'])
  setTimeout(() => addPacket('接收', '按键查询结果', response), 100)
}

function setKey() {
  const data = [keyId.value, keyType.value, systemKeyIndex.value]
  const packet = buildPacket('81', '91', '03', data)
  addPacket('发送', `设置按键${getKeyName(keyId.value)}为系统功能`, packet)
  setTimeout(() => addPacket('接收', '按键设置成功', packet), 100)
  ElMessage.success('按键设置成功')
}

function setMediaKey() {
  const mediaValues = ['8301', 'B700', 'CD00', 'B600', 'B500', 'E200', 'E900', 'EA00']
  const value = mediaValues[parseInt(mediaKeyIndex.value)]
  const data = [keyId.value, keyType.value, mediaKeyIndex.value, value.slice(0, 2), value.slice(2, 4)]
  const packet = buildPacket('81', '91', '05', data)
  addPacket('发送', `设置多媒体键`, packet)
  setTimeout(() => addPacket('接收', '多媒体键设置成功', packet), 100)
  ElMessage.success('多媒体键设置成功')
}

function setComboKey() {
  if (!comboKeys.value) {
    ElMessage.error('请输入组合键值')
    return
  }
  const comboArray = comboKeys.value.split(' ').filter(v => v)
  const data = [keyId.value, keyType.value, '00', ...comboArray]
  const packet = buildPacket('81', '91', (3 + comboArray.length).toString(16).padStart(2, '0'), data)
  addPacket('发送', `设置组合键`, packet)
  setTimeout(() => addPacket('接收', '组合键设置成功', packet), 100)
  ElMessage.success('组合键设置成功')
}

function setFireKey() {
  const data = [keyId.value, keyType.value, '00', fireInterval.value, fireCount.value, '02']
  const packet = buildPacket('81', '91', '06', data)
  addPacket('发送', `设置火力键`, packet)
  setTimeout(() => addPacket('接收', '火力键设置成功', packet), 100)
  ElMessage.success('火力键设置成功')
}

// 板载配置
function setProfile() {
  const packet = buildPacket('81', '92', '01', [profileId.value])
  addPacket('发送', `切换到配置${parseInt(profileId.value) + 1}`, packet)
  setTimeout(() => addPacket('接收', '配置切换成功', packet), 100)
  ElMessage.success(`已切换到配置${parseInt(profileId.value) + 1}`)
}

function queryProfile() {
  const packet = buildPacket('80', '92', '00', [])
  addPacket('发送', '查询配置', packet)
  const response = buildPacket('81', '92', '01', [profileId.value])
  setTimeout(() => addPacket('接收', '配置查询结果', response), 100)
}

// 恢复默认设置
function resetSettings() {
  const packet = buildPacket('81', '93', '01', [resetType.value])
  const typeName = resetType.value === '00' ? '所有设置' : '按键设置'
  addPacket('发送', `恢复默认:${typeName}`, packet)
  setTimeout(() => addPacket('接收', '恢复默认成功', packet), 100)
  ElMessage.warning(`已恢复默认${typeName}`)
}

// 系统信息
function queryVersion(type) {
  const packet = buildPacket('80', 'F0', '01', [type])
  const deviceName = type === '00' ? '鼠标' : '接收器'
  addPacket('发送', `查询${deviceName}版本`, packet)
  // 模拟返回
  const versionData = ['31', '2E', '30', '30', '31', '00']
  const response = buildPacket('81', 'F0', '06', [type, ...versionData])
  setTimeout(() => addPacket('接收', `${deviceName}版本:1.001`, response), 100)
}

function queryMode() {
  const packet = buildPacket('80', 'F1', '00', [])
  addPacket('发送', '查询工作模式', packet)
  // 模拟返回
  const modes = ['00', '01', '02']
  const modeNames = ['有线', '无线', '蓝牙']
  const randomMode = modes[Math.floor(Math.random() * modes.length)]
  const response = buildPacket('81', 'F1', '01', [randomMode])
  setTimeout(() => addPacket('接收', `工作模式:${modeNames[parseInt(randomMode)]}`, response), 100)
}

// 批量操作
function getAllSettings() {
  ElMessage.info('正在获取所有设置...')
  queryDpi()
  setTimeout(() => queryProfile(), 200)
  setTimeout(() => queryVersion('00'), 400)
  setTimeout(() => queryMode(), 600)
}

function clearAll() {
  packetList.value = []
  ElMessage.success('显示已清空')
}

// 辅助函数
function getKeyName(keyId) {
  const names = ['左键', '右键', '中键', '前进键', '后退键', 'DPI键']
  return names[parseInt(keyId)] || '未知'
}

function getCommandDesc(cmd) {
  const commands = {
    '90': 'DPI设置',
    '91': '按键修改',
    '92': '板载配置',
    '93': '恢复默认',
    'F0': '版本号',
    'F1': '工作模式'
  }
  return commands[cmd] || '未知命令'
}

function formatPacket(packetStr) {
  return packetStr.split(' ')
}

function getByteClass(index) {
  if (index === 0) return 'byte-tag report-id'
  if (index === 1) return 'byte-tag rw-control'
  if (index === 2) return 'byte-tag command-type'
  if (index === 3) return 'byte-tag data-length'
  if (index === 16) return 'byte-tag packet-crc'
  return 'byte-tag data'
}

// 宏编辑功能
function addMacroStep() {
  currentMacro.steps.push({
    type: '02',
    value: '04',
    duration: '0064'
  })
}

function removeMacroStep(index) {
  currentMacro.steps.splice(index, 1)
}

function saveMacro() {
  if (!keyId.value) {
    ElMessage.error('请先选择按键')
    return
  }
  // 这里简化处理，实际应该构建完整的宏数据包
  const packet = buildPacket('81', '91', '08', [
    keyId.value,
    '0C',
    '01',
    currentMacro.repeatType,
    '02',
    '04',
    '00',
    '64'
  ])
  addPacket('发送', '设置宏', packet)
  macroDialogVisible.value = false
  ElMessage.success('宏设置已保存')
}

// 初始化
addPacket('系统', '初始化', buildPacket('80', 'F0', '01', ['00']))
</script>

<style scoped>
.container {
  padding: 20px;
  max-width: 1400px;
  margin: 0 auto;
}

h1 {
  text-align: center;
  color: #409EFF;
  margin-bottom: 20px;
}

.info-card {
  margin-bottom: 20px;
}

.info-row {
  display: inline-block;
  margin-right: 30px;
  margin-bottom: 10px;
}

.info-label {
  font-weight: bold;
  color: #606266;
  margin-right: 10px;
}

.info-value {
  color: #409EFF;
  font-family: monospace;
}

.function-card {
  margin-bottom: 20px;
}

.card-title {
  font-size: 16px;
  font-weight: bold;
}

.packet-card {
  margin-top: 20px;
}

.packet-data {
  font-family: monospace;
  font-size: 14px;
}

.packet-detail {
  margin: 20px 0;
}

.detail-row {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  padding: 8px;
  background-color: #f5f7fa;
  border-radius: 4px;
}

.detail-label {
  font-weight: bold;
  width: 100px;
  color: #606266;
}

.detail-value {
  font-family: monospace;
  font-size: 14px;
  color: #409EFF;
  margin-right: 20px;
}

.detail-desc {
  color: #909399;
  font-size: 12px;
}

.macro-steps {
  max-height: 300px;
  overflow-y: auto;
  padding: 10px;
  background-color: #f5f7fa;
  border-radius: 4px;
}

.macro-step {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  padding: 8px;
  background-color: white;
  border-radius: 4px;
}

h4 {
  margin: 10px 0;
  color: #606266;
}
</style>