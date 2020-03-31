<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template v-slot:lead>
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4" />

            <b-list-group>
                <b-list-group-item
                    v-for="(answer, index) in answers"
                    :key="index"
                    @click="selectAnswer(index)"
                    :class="answerClass(index)"
                >
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button
                @click="submitAnswer"
                variant="primary"
                :disabled="selectedIndex === null || answered">
                Submit
            </b-button>
            <b-button
                @click="next"
                variant="success"
                :disabled="!answered">
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function
    },
    data() {
        return {
            selectedIndex: null,
            shuffledAnswers: [],
            correctIndex: null,
            answered: false,
        }
    },
    computed: {
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)
            return answers
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler() {
                this.selectedIndex = null
                this.answered = false
                this.shuffleAnswers()
            }
        }
    },
    methods: {
        selectAnswer(index) {
            if (this.answered === false) {
                this.selectedIndex = index
            }
        },
        submitAnswer() {
            let isCorrect = false

            if (this.selectedIndex === this.correctIndex) {
                isCorrect = true
            }
            this.answered = true

            this.increment(isCorrect)
        },
        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        answerClass(index) {
            let answerClass = ''

            if (!this.answered && this.selectedIndex === index) {
                answerClass += 'selected'
            } else if (this.answered && this.correctIndex === index) {
                answerClass += 'correct'
            } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                answerClass += 'incorrect'
            }

            if (!this.answered) {
                answerClass += ' selectable'
            }

            return answerClass
        }
    }
}
</script>

<style scoped>
.jumbotron {
    margin-top: 50px;
}

.list-group {
    margin-bottom: 15px;
}

.selectable:hover {
    background-color: #EEE;
    cursor: pointer;
}

.btn {
    margin: 0 5px;
}

.selected {
    background-color: lightblue;
}

.correct {
    background-color: lightgreen;
}

.incorrect {
    background-color: pink;
}
</style>