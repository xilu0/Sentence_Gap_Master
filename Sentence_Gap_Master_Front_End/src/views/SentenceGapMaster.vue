<script setup>
import { ref } from 'vue'
import { choose, submitQuestion } from '@/api/api.js'
import { ElMessageBox } from 'element-plus'

const step = ref(1)

const titleList = ref([
    {
        id: 1,
        title: 'a',
    },
    {
        id: 2,
        title: 'b',
    },
    {
        id: 3,
        title: 'c',
    },
])

const questionList = ref([
    {
        questionId: 1,
        user_choose: null,
        prefix: 'Apple',
        subfix: 'red.',
        options: [
            {
                optionId: 1,
                value: 'is',
            },
            {
                optionId: 2,
                value: 'are',
            },
            {
                optionId: 3,
                value: 'am',
            },
            {
                optionId: 4,
                value: 'was',
            },
        ],
    },
    {
        questionId: 2,
        user_choose: null,
        prefix: 'Orange',
        subfix: 'yellow.',
        options: [
            {
                optionId: 1,
                value: 'is',
            },
            {
                optionId: 2,
                value: 'are',
            },
            {
                optionId: 3,
                value: 'am',
            },
            {
                optionId: 4,
                value: 'was',
            },
        ],
    },
    {
        questionId: 3,
        user_choose: null,
        prefix: 'Blueberry',
        subfix: 'blue.',
        options: [
            {
                optionId: 1,
                value: 'is',
            },
            {
                optionId: 2,
                value: 'are',
            },
            {
                optionId: 3,
                value: 'am',
            },
            {
                optionId: 4,
                value: 'was',
            },
        ],
    },
])

const questionIndex = ref(0)

const chooseTitle = async (id) => {
    const { data } = await choose(id)
    console.log(data)
    questionList.value = data
    questionIndex.value = 0
    step.value = 2
}

const submit = async () => {
    console.log(questionList.value)
    await ElMessageBox.confirm('您确认交卷吗', '提示', {
        confirmButtonText: '是',
        cancelButtonText: '否',
    })
    const { data } = await submitQuestion(questionList.value)
    step.value = 3
    console.log(data)
}

const gameResult = ref(true)

const getLetter = (index) => {
    if (index === 0) {
        return 'A'
    } else if (index === 1) {
        return 'B'
    } else if (index === 2) {
        return 'C'
    } else if (index === 3) {
        return 'D'
    }
}

const getType = (id) => {
    const { user_choose } = questionList.value[questionIndex.value]
    if (user_choose === id) {
        return 'primary'
    } else {
        return ''
    }
}

const chooseOption = (item) => {
    const question = questionList.value[questionIndex.value]
    question.user_choose = item.optionId
}
</script>

<template>
    <div v-if="step === 1">
        <div>请选择您要挑战的文章</div>
        <div v-for="item in titleList" :key="item.id" class="choose-title-button">
            <el-button @click="chooseTitle(item.id)">
                {{ item.id + ' ' + item.title }}
            </el-button>
        </div>
    </div>
    <div v-if="step === 2">
        <div class="question-box">
            <span>{{ questionList[questionIndex].prefix + ' ' }}</span>
            <span style="border-bottom: 1px solid black">&nbsp;</span>
            <span>{{ ' ' + questionList[questionIndex].subfix }}</span>
            <span></span>
        </div>
        <el-divider />
        <div class="choose-box">
            <div>试题进度：{{ questionIndex + 1 }} / {{ questionList.length }}</div>
            <div class="option-box">
                <div
                    v-for="(item, index) in questionList[questionIndex].options"
                    :key="index"
                    style="margin-top: 10px"
                >
                    <el-button @click="chooseOption(item)" :type="getType(item.optionId)"
                        >{{ getLetter(index) }}. {{ item.value }}</el-button
                    >
                </div>
            </div>
            <div class="goto-next-question-button">
                <el-button v-if="questionIndex > 0" @click="questionIndex--">上一题</el-button>
                <el-button
                    v-if="questionIndex < questionList.length - 1"
                    type="primary"
                    @click="questionIndex++"
                    >下一题</el-button
                >
            </div>
            <div v-if="questionIndex === questionList.length - 1" class="submit-button">
                <el-button type="primary" @click="submit">交卷</el-button>
            </div>
        </div>
    </div>
    <div v-if="step === 3">
        <div v-if="gameResult">恭喜你！挑战成功！🎉</div>
        <div v-if="!gameResult">别放弃！你正在进步！🚀</div>
        <div class="again-button">
            <el-button type="primary" @click="step = 1">再来一次</el-button>
        </div>
    </div>
</template>

<style scoped>
.choose-title-button {
    margin-top: 20px;
}

.goto-next-question-button {
    margin-top: 10px;
}

.submit-button {
    margin-top: 10px;
}

.option-box {
    margin-top: 10px;
}

.again-button {
    margin-top: 10px;
}
</style>
