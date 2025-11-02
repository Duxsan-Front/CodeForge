<template>
  <div class="course-page">
    <!-- Мобильный хедер -->
    <MobileHeader
      :title="courseTitle"
      :progress="progress"
      :is-mobile="isMobile"
      @toggle-sidebar="showSidebar = true"
    />

    <div class="course-layout">
      <!-- Боковая панель с заданиями -->
      <LessonSidebar
        :lessons="lessons"
        :current-lesson="currentLesson"
        :progress="progress"
        :title="courseTitle"
        :is-mobile="isMobile"
        @select-lesson="selectLesson"
      />

      <!-- Основной контент -->
      <main class="main-content">
        <ProblemSection :lesson="currentLesson" />
        
        <CodeSection
          v-model:code="userCode"
          :line-count="lineCount"
          @reset="resetCode"
        />
        
        <ControlPanel
          :is-running="isRunning"
          :is-testing="isTesting"
          :is-submitting="isSubmitting"
          @run="runCode"
          @test="runTests"
          @submit="submitSolution"
          @save="saveCode"
        />
        
        <InputSection v-model:input="consoleInput" />
        
        <OutputSection
          :output="consoleOutput"
          @clear="clearOutput"
        />
        
        <TestsSection
          :tests="currentLesson.tests || []"
          :passed-tests="passedTests"
        />
      </main>
    </div>

    <!-- Мобильный сайдбар -->
    <MobileSidebar
      v-if="isMobile && showSidebar"
      :lessons="lessons"
      :current-lesson="currentLesson"
      :title="courseTitle"
      @select-lesson="selectLessonMobile"
      @close="showSidebar = false"
    />
  </div>
</template>

<script>
import MobileHeader from './MobileHeader.vue'
import LessonSidebar from './LessonSidebar.vue'
import MobileSidebar from './MobileSidebar.vue'
import ProblemSection from './ProblemSection.vue'
import CodeSection from './CodeSection.vue'
import ControlPanel from './ControlPanel.vue'
import InputSection from './InputSection.vue'
import OutputSection from './OutputSection.vue'
import TestsSection from './TestsSection.vue'

export default {
  name: 'CourseLayout',
  components: {
    MobileHeader,
    LessonSidebar,
    MobileSidebar,
    ProblemSection,
    CodeSection,
    ControlPanel,
    InputSection,
    OutputSection,
    TestsSection
  },
  props: {
    courseTitle: {
      type: String,
      required: true
    },
    lessons: {
      type: Array,
      required: true
    },
    language: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      userCode: '',
      consoleInput: '',
      consoleOutput: '',
      isRunning: false,
      isTesting: false,
      isSubmitting: false,
      progress: 10,
      currentLesson: {},
      showSidebar: false,
      isMobile: false
    }
  },
  computed: {
    passedTests() {
      if (!this.currentLesson.tests) return 0
      return this.currentLesson.tests.filter(test => test.status === 'passed').length
    },
    lineCount() {
      return Math.max(this.userCode.split('\n').length, 1)
    }
  },
  mounted() {
    this.checkMobile()
    this.selectLesson(this.lessons[0])
    window.addEventListener('resize', this.checkMobile)
    this.loadSavedCode()
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.checkMobile)
  },
  methods: {
    checkMobile() {
      this.isMobile = window.innerWidth <= 1024
    },
    selectLesson(lesson) {
      if (this.currentLesson.id === lesson.id) return
      this.currentLesson = { 
        ...lesson,
        tests: lesson.tests ? lesson.tests.map(test => ({ ...test })) : []
      }
      this.userCode = lesson.starterCode || ''
      this.consoleInput = ''
      this.consoleOutput = ''
      this.resetTests()
      this.loadSavedCode()
    },
    selectLessonMobile(lesson) {
      this.selectLesson(lesson)
      this.showSidebar = false
    },
    resetTests() {
      if (this.currentLesson.tests) {
        this.currentLesson.tests.forEach(test => {
          test.status = null
          test.actual = null
          test.error = null
        })
      }
    },
    resetCode() {
      this.userCode = this.currentLesson.starterCode || ''
    },
    async runCode() {
      this.isRunning = true
      this.consoleOutput = 'Запуск программы...\n\n'
      
      try {
        await new Promise(resolve => setTimeout(resolve, 1000))
        
        // Симуляция выполнения кода с пользовательским вводом
        if (this.userCode.includes('input()')) {
          if (this.consoleInput.trim()) {
            const inputs = this.consoleInput.split('\n').filter(line => line.trim())
            let output = ''
            
            // Простая симуляция выполнения кода с подстановкой ввода
            if (this.userCode.includes('int(input())')) {
              const numbers = inputs.map(line => line.split(' ').map(Number)).flat()
              if (numbers.length >= 2) {
                output = `Сумма: ${numbers[0] + numbers[1]}`
              }
            } else if (this.userCode.includes('n = int(input())')) {
              const n = Number(inputs[0])
              if (this.userCode.includes('factorial') || this.userCode.includes('*')) {
                let factorial = 1
                for (let i = 1; i <= n; i++) factorial *= i
                output = `Факториал: ${factorial}`
              }
            }
            
            this.consoleOutput += `Входные данные: ${inputs.join(', ')}\n`
            this.consoleOutput += `Результат: ${output}\n`
          } else {
            this.consoleOutput += 'Ожидание ввода данных...\n'
          }
        } else if (this.userCode.includes('print("Hello, World!")')) {
          this.consoleOutput += 'Hello, World!\n'
        } else if (this.userCode.includes('console.log("Hello, World!")')) {
          this.consoleOutput += 'Hello, World!\n'
        }
        
        this.consoleOutput += '\nПрограмма завершена.'
      } catch (error) {
        this.consoleOutput += `Ошибка: ${error.message}\n`
      } finally {
        this.isRunning = false
      }
    },
    async runTests() {
      this.isTesting = true
      this.resetTests()
      this.consoleOutput = ''
      
      for (let i = 0; i < this.currentLesson.tests.length; i++) {
        const test = this.currentLesson.tests[i]
        test.status = 'running'
        
        await new Promise(resolve => setTimeout(resolve, 500))
        
        try {
          // Симуляция выполнения теста
          if (this.currentLesson.id === 1) {
            if (this.language === 'python') {
              test.actual = 'Hello, World!'
            } else if (this.language === 'javascript') {
              test.actual = 'Hello, World!'
            }
          } else if (this.currentLesson.id === 2) {
            const [a, b] = test.input.split(' ').map(Number)
            test.actual = (a + b).toString()
          }
          // ... остальная логика тестов
          
          test.status = test.actual === test.expected ? 'passed' : 'failed'
          
        } catch (error) {
          test.status = 'failed'
          test.error = error.message
        }
      }
      
      this.isTesting = false
    },
    async submitSolution() {
      this.isSubmitting = true
      this.consoleOutput = 'Проверка решения...\n\n'
      
      await this.runTests()
      
      if (this.passedTests === this.currentLesson.tests.length) {
        const lessonIndex = this.lessons.findIndex(l => l.id === this.currentLesson.id)
        if (lessonIndex !== -1) {
          this.lessons[lessonIndex].completed = true
          this.progress = Math.round((this.lessons.filter(l => l.completed).length / this.lessons.length) * 100)
        }
        
        this.consoleOutput += '\n🎉 Поздравляем! Все тесты пройдены! Решение принято.\n'
      } else {
        this.consoleOutput += '\n❌ Не все тесты пройдены. Продолжайте работать!\n'
      }
      
      this.isSubmitting = false
    },
    saveCode() {
      localStorage.setItem(`${this.language}_lesson_${this.currentLesson.id}`, this.userCode)
      this.consoleOutput = 'Код сохранен локально.\n'
    },
    loadSavedCode() {
      const savedCode = localStorage.getItem(`${this.language}_lesson_${this.currentLesson.id}`)
      if (savedCode) {
        this.userCode = savedCode
      }
    },
    clearOutput() {
      this.consoleOutput = ''
    }
  }
}
</script>

<style scoped>
.course-page {
  background-color: #0E1117;
  color: #E2E8F0;
  padding: 20px;
}

.course-layout {
  display: grid;
  grid-template-columns: 340px 1fr;
  gap: 20px;
  align-items: start;
}

/* Кастомные скроллбары */
::-webkit-scrollbar {
  width: 12px;
  height: 12px;
}

::-webkit-scrollbar-track {
  background: #1E1E1E;
  border-radius: 6px;
}

::-webkit-scrollbar-thumb {
  background: linear-gradient(135deg, #3B82F6, #8B5CF6);
  border-radius: 6px;
  border: 2px solid #1E1E1E;
  transition: all 0.3s ease;
}

::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(135deg, #2563EB, #7C3AED);
  transform: scale(1.05);
}

::-webkit-scrollbar-thumb:active {
  background: linear-gradient(135deg, #1D4ED8, #6D28D9);
}

* {
  scrollbar-width: thin;
  scrollbar-color: #3B82F6 #1E1E1E;
}

.main-content {
  padding: 0;
  overflow-y: auto;
  max-height: calc(100vh - 140px);
  margin-top: 0;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.main-content::-webkit-scrollbar {
  width: 10px;
}

.main-content::-webkit-scrollbar-track {
  background: #0E1117;
  border-radius: 6px;
}

.main-content::-webkit-scrollbar-thumb {
  background: linear-gradient(135deg, #3B82F6, #8B5CF6);
  border-radius: 6px;
  border: 2px solid #0E1117;
}

@media (max-width: 1024px) {
  .course-layout {
    grid-template-columns: 1fr;
  }
  
  .main-content {
    overflow-y: visible;
    max-height: none;
  }
}

@media (max-width: 768px) {
  .course-page {
    padding: 10px;
  }
}

@media (max-width: 480px) {
  .course-page {
    padding: 5px;
  }
}
</style>