<script setup lang="ts">
import { onBeforeMount, ref } from 'vue'

const mass = ref<number>(0)
const massConcentration = ref(0)
const molarQuantity = ref(0)
const molarConcentration = ref(0)
const volumn = ref(0)
const nuclearType = ref<'DNA' | 'RNA'>('DNA')
const length = ref(160)
const molarMass = ref(0)

const massUnitVar = 'ng'
const massConcentrationUnitVar = 'ng/ul'
const molarMassUnitVar = 'nmol'
const molarConcentrationUnitVar = 'nM'
const volumnUnitVar = 'ul'

onBeforeMount(() => {
    length.value = Number(localStorage.getItem('length')) || 160
    nuclearType.value = (localStorage.getItem('nuclearType') as 'DNA' | 'RNA') || 'DNA'
    handleNuclearTypeChange(nuclearType.value)
})

const handleNuclearTypeChange = (value: 'DNA' | 'RNA') => {
    molarMass.value = value === 'DNA' ? 660 * length.value : 340 * length.value
    localStorage.setItem('nuclearType', value)
}

const handleLengthChange = () => {
    localStorage.setItem('length', length.value.toString())
    handleNuclearTypeChange(nuclearType.value)
}

const handleClear = () => {
    mass.value = 0
    massConcentration.value = 0
    molarQuantity.value = 0
    molarConcentration.value = 0
    volumn.value = 0
}

const handleCalculate = () => {
    if (molarConcentration.value > 0) {
        massConcentration.value = molarConcentration.value * molarMass.value * 1e-6
        if (volumn.value > 0) {
            molarQuantity.value = molarConcentration.value * volumn.value * 1e-6
            mass.value = massConcentration.value * volumn.value
        }
    }
    else if (massConcentration.value > 0) {
        molarConcentration.value = massConcentration.value / (molarMass.value * 1e-6)
        if (volumn.value > 0) {
            mass.value = massConcentration.value * volumn.value
            molarQuantity.value = molarConcentration.value * volumn.value * 1e-6
        }
    }
    else if (molarQuantity.value > 0) {
        mass.value = molarQuantity.value * molarMass.value
        if (volumn.value > 0) {
            molarConcentration.value = molarQuantity.value / (volumn.value * 1e-6)
            massConcentration.value = molarConcentration.value * molarMass.value * 1e-6
        }
    }
    else if (mass.value > 0) {
        molarQuantity.value = mass.value / (molarMass.value )
        if (volumn.value > 0) {
            molarConcentration.value = molarQuantity.value / (volumn.value * 1e-6)
            massConcentration.value = molarConcentration.value * molarMass.value * 1e-6
        }
    }
}
</script>

<template>
    <div class="container" style="align-items: center; justify-content: center; display: flex; flex-direction: column;">
        <el-form label-position="left" label-width="100px" size="mini">
            <el-form-item label="长度">
                <el-input-number
                    v-model="length"
                    :min="1"
                    :max="100000"
                    type="number"
                    placeholder="请输入长度"
                    @change="handleLengthChange"
                />
            </el-form-item>
            <el-form-item label="核酸类型">
                <el-radio-group v-model="nuclearType" @change="handleNuclearTypeChange">
                    <el-radio label="DNA">DNA</el-radio>
                    <el-radio label="RNA">RNA</el-radio>
                </el-radio-group>
            </el-form-item>
            <el-form-item label="摩尔质量">
                <el-input v-model="molarMass" disabled placeholder="摩尔质量" style="width: 100%" />
            </el-form-item>
            <el-form-item label="质量">
                <div style="display: flex; justify-content: space-between; align-items: center">
                    <el-input v-model="mass" placeholder="质量">
                        <template #append>
                            {{ massUnitVar }}
                        </template>
                    </el-input>
                </div>
            </el-form-item>
            <el-form-item label="质量浓度">
                <div style="display: flex; justify-content: space-between; align-items: center">
                    <el-input v-model="massConcentration" placeholder="质量浓度">
                        <template #append>
                            {{ massConcentrationUnitVar }}
                        </template>
                    </el-input>
                </div>
            </el-form-item>
            <el-form-item label="摩尔量">
                <div style="display: flex; justify-content: space-between; align-items: center">
                    <el-input v-model="molarQuantity" placeholder="摩尔量">
                        <template #append>
                            {{ molarMassUnitVar }}
                        </template>
                    </el-input>
                </div>
            </el-form-item>
            <el-form-item label="摩尔浓度">
                <div style="display: flex; justify-content: space-between; align-items: center">
                    <el-input v-model="molarConcentration"  placeholder="摩尔浓度">
                        <template #append>
                            {{ molarConcentrationUnitVar }}
                        </template>
                    </el-input>
                </div>
            </el-form-item>
            <el-form-item label="体积">
                <div style="display: flex; justify-content: space-between; align-items: center">
                    <el-input v-model="volumn" placeholder="体积">
                        <template #append>
                            {{ volumnUnitVar }}
                        </template>
                    </el-input>
                </div>
            </el-form-item>
            <el-button type="primary" @click="handleCalculate"> 计算 </el-button>
            <el-button type="primary" @click="handleClear"> 清空 </el-button>
        </el-form>
    </div>
</template>

<style scoped>
.logo.vite:hover {
    filter: drop-shadow(0 0 2em #747bff);
}

.logo.vue:hover {
    filter: drop-shadow(0 0 2em #249b73);
}
</style>
