<template>
  <div class="course-page">
    <div class="course-layout">
      <!-- Боковая панель с заданиями -->
      <aside class="sidebar">
        <div class="sidebar-content">
          <div class="sidebar-header">
            <h2>Python Курс</h2>
            <div class="progress">
              <div class="progress-bar">
                <div class="progress-fill" :style="{ width: progress + '%' }"></div>
              </div>
              <span>{{ progress }}% завершено</span>
            </div>
          </div>
          
          <div class="lessons-list">
            <div
              v-for="lesson in lessons" 
              :key="lesson.id"
              class="lesson-item"
              :class="{
                'lesson-item--active': currentLesson.id === lesson.id,
                'lesson-item--completed': lesson.completed
              }"
              @click="selectLesson(lesson)"
            >
              <div class="lesson-icon">
                <span v-if="lesson.completed">✓</span>
                <span v-else>{{ lesson.id }}</span>
              </div>
              <div class="lesson-info">
                <h3>{{ lesson.title }}</h3>
                <p>{{ lesson.description }}</p>
              </div>
            </div>
          </div>
        </div>
      </aside>

      <!-- Основной контент -->
      <main class="main-content">
        <!-- Условие задачи -->
        <section class="problem-section">
          <div class="problem-header">
            <h1>{{ currentLesson.title }}</h1>
            <div class="difficulty" :class="'difficulty--' + currentLesson.difficulty">
              {{ difficultyText[currentLesson.difficulty] }}
            </div>
          </div>
          <div class="problem-description">
            <p>{{ currentLesson.description }}</p>
          </div>
        </section>

        <!-- Простой редактор кода -->
        <section class="code-section">
          <div class="code-header">
            <h3>Редактор кода</h3>
            <div class="code-actions">
              <button class="btn btn--small" @click="resetCode">Сбросить</button>
            </div>
          </div>
          <div class="code-editor-container">
            <div class="code-editor-wrapper">
              <textarea 
                v-model="userCode"
                class="simple-editor"
                placeholder="Введите ваш код здесь..."
                spellcheck="false"
              ></textarea>
              <div class="line-numbers">
                <span v-for="n in lineCount" :key="n">{{ n }}</span>
              </div>
            </div>
          </div>
        </section>

        <!-- Панель управления -->
        <section class="control-panel">
          <div class="control-buttons">
            <button 
              class="btn btn--primary" 
              @click="runCode"
              :disabled="isRunning"
            >
              <span v-if="isRunning">⏳</span>
              🚀 Запустить
            </button>
            <button 
              class="btn btn--secondary" 
              @click="runTests"
              :disabled="isTesting"
            >
              <span v-if="isTesting">⏳</span>
              ✅ Проверить тесты
            </button>
            <button 
              class="btn btn--success" 
              @click="submitSolution"
              :disabled="isSubmitting"
            >
              <span v-if="isSubmitting">⏳</span>
              📤 Отправить решение
            </button>
            <button 
              class="btn btn--outline" 
              @click="saveCode"
            >
              💾 Сохранить
            </button>
          </div>
        </section>

        <!-- Консольный вывод -->
        <section class="output-section">
          <div class="output-header">
            <h3>Консольный вывод</h3>
            <button class="btn btn--small" @click="clearOutput">Очистить</button>
          </div>
          <div class="console-output">
            <pre>{{ consoleOutput }}</pre>
          </div>
        </section>

        <!-- Результаты тестов -->
        <section class="tests-section">
          <div class="tests-header">
            <h3>Тесты</h3>
            <div class="tests-summary">
              <span class="tests-passed">Пройдено: {{ passedTests }}/{{ currentLesson.tests ? currentLesson.tests.length : 0 }}</span>
            </div>
          </div>
          <div class="tests-list">
            <div 
              v-for="(test, index) in (currentLesson.tests || [])" 
              :key="index"
              class="test-item"
              :class="{
                'test-item--passed': test.status === 'passed',
                'test-item--failed': test.status === 'failed',
                'test-item--running': test.status === 'running'
              }"
            >
              <div class="test-icon">
                <span v-if="test.status === 'passed'">✓</span>
                <span v-else-if="test.status === 'failed'">✗</span>
                <span v-else-if="test.status === 'running'">⏳</span>
                <span v-else>○</span>
              </div>
              <div class="test-content">
                <div class="test-input">
                  <strong>Вход:</strong> {{ test.input }}
                </div>
                <div class="test-expected">
                  <strong>Ожидается:</strong> {{ test.expected }}
                </div>
                <div v-if="test.actual" class="test-actual">
                  <strong>Получено:</strong> {{ test.actual }}
                </div>
                <div v-if="test.error" class="test-error">
                  <strong>Ошибка:</strong> {{ test.error }}
                </div>
              </div>
            </div>
          </div>
        </section>
      </main>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Python',
  data() {
    return {
      userCode: '',
      consoleOutput: '',
      isRunning: false,
      isTesting: false,
      isSubmitting: false,
      progress: 25,
      currentLesson: {
        tests: []
      },
      lessons: [
        {
          id: 1,
          title: 'Привет, Python!',
          description: 'Напишите программу, которая выводит "Hello, World!" на экран.',
          difficulty: 'easy',
          completed: true,
          tests: [
            { input: '', expected: 'Hello, World!', actual: null, status: null, error: null },
            { input: 'test', expected: 'Hello, World!', actual: null, status: null, error: null }
          ],
          starterCode: 'print("Hello, World!")'
        },
        {
          id: 2,
          title: 'Сумма двух чисел',
          description: 'Напишите программу, которая принимает два числа и выводит их сумму.',
          difficulty: 'easy',
          completed: false,
          tests: [
            { input: '5 3', expected: '8', actual: null, status: null, error: null },
            { input: '10 20', expected: '30', actual: null, status: null, error: null },
            { input: '-5 5', expected: '0', actual: null, status: null, error: null }
          ],
          starterCode: '# Введите ваш код здесь\nnum1 = int(input())\nnum2 = int(input())\nprint(num1 + num2)'
        },
        {
          id: 3,
          title: 'Факториал числа',
          description: 'Напишите программу, которая вычисляет факториал заданного числа.',
          difficulty: 'medium',
          completed: false,
          tests: [
            { input: '5', expected: '120', actual: null, status: null, error: null },
            { input: '0', expected: '1', actual: null, status: null, error: null },
            { input: '7', expected: '5040', actual: null, status: null, error: null }
          ],
          starterCode: '# Вычислите факториал числа\nn = int(input())\n# Ваш код здесь'
        },
        {
          id: 4,
          title: 'Проверка на четность',
          description: 'Напишите программу, которая проверяет, является ли число четным.',
          difficulty: 'easy',
          completed: false,
          tests: [
            { input: '4', expected: 'Четное', actual: null, status: null, error: null },
            { input: '7', expected: 'Нечетное', actual: null, status: null, error: null },
            { input: '0', expected: 'Четное', actual: null, status: null, error: null }
          ],
          starterCode: '# Проверка на четность\nn = int(input())\n# Ваш код здесь'
        },
        {
          id: 5,
          title: 'Поиск максимума',
          description: 'Напишите программу, которая находит максимальное из трех чисел.',
          difficulty: 'medium',
          completed: false,
          tests: [
            { input: '5 3 8', expected: '8', actual: null, status: null, error: null },
            { input: '10 20 15', expected: '20', actual: null, status: null, error: null },
            { input: '-5 -3 -1', expected: '-1', actual: null, status: null, error: null }
          ],
          starterCode: '# Найдите максимальное из трех чисел\nnum1, num2, num3 = map(int, input().split())\n# Ваш код здесь'
        },
        {
          id: 6,
          title: 'Палиндром',
          description: 'Напишите программу, которая проверяет, является ли строка палиндромом.',
          difficulty: 'medium',
          completed: false,
          tests: [
            { input: 'radar', expected: 'Палиндром', actual: null, status: null, error: null },
            { input: 'python', expected: 'Не палиндром', actual: null, status: null, error: null },
            { input: 'a', expected: 'Палиндром', actual: null, status: null, error: null }
          ],
          starterCode: '# Проверка на палиндром\ns = input()\n# Ваш код здесь'
        },
        {
          id: 7,
          title: 'Числа Фибоначчи',
          description: 'Напишите программу, которая выводит n-ное число Фибоначчи.',
          difficulty: 'medium',
          completed: false,
          tests: [
            { input: '5', expected: '5', actual: null, status: null, error: null },
            { input: '7', expected: '13', actual: null, status: null, error: null },
            { input: '10', expected: '55', actual: null, status: null, error: null }
          ],
          starterCode: '# Числа Фибоначчи\nn = int(input())\n# Ваш код здесь'
        },
        {
          id: 8,
          title: 'Простые числа',
          description: 'Напишите программу, которая проверяет, является ли число простым.',
          difficulty: 'medium',
          completed: false,
          tests: [
            { input: '7', expected: 'Простое', actual: null, status: null, error: null },
            { input: '10', expected: 'Составное', actual: null, status: null, error: null },
            { input: '17', expected: 'Простое', actual: null, status: null, error: null }
          ],
          starterCode: '# Проверка на простое число\nn = int(input())\n# Ваш код здесь'
        },
        {
          id: 9,
          title: 'Обратная строка',
          description: 'Напишите программу, которая переворачивает строку.',
          difficulty: 'easy',
          completed: false,
          tests: [
            { input: 'hello', expected: 'olleh', actual: null, status: null, error: null },
            { input: 'python', expected: 'nohtyp', actual: null, status: null, error: null },
            { input: 'a', expected: 'a', actual: null, status: null, error: null }
          ],
          starterCode: '# Переверните строку\ns = input()\n# Ваш код здесь'
        },
        {
          id: 10,
          title: 'Сортировка чисел',
          description: 'Напишите программу, которая сортирует три числа по возрастанию.',
          difficulty: 'medium',
          completed: false,
          tests: [
            { input: '5 3 8', expected: '3 5 8', actual: null, status: null, error: null },
            { input: '10 20 15', expected: '10 15 20', actual: null, status: null, error: null },
            { input: '-5 -3 -1', expected: '-5 -3 -1', actual: null, status: null, error: null }
          ],
          starterCode: '# Сортировка трех чисел\nnum1, num2, num3 = map(int, input().split())\n# Ваш код здесь'
        }
      ],
      difficultyText: {
        easy: 'Легко',
        medium: 'Средне',
        hard: 'Сложно'
      }
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
    this.selectLesson(this.lessons[0])
  },
  methods: {
    selectLesson(lesson) {
      if (this.currentLesson.id === lesson.id) return
      this.currentLesson = { 
        ...lesson,
        tests: lesson.tests.map(test => ({ ...test }))
      }
      this.userCode = lesson.starterCode || ''
      this.consoleOutput = ''
      this.resetTests()
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
        
        if (this.userCode.includes('print("Hello, World!")')) {
          this.consoleOutput += 'Hello, World!\n'
        } else if (this.userCode.includes('input()')) {
          this.consoleOutput += 'Программа ожидает ввод данных...\n'
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
      
      for (let i = 0; i < this.currentLesson.tests.length; i++) {
        const test = this.currentLesson.tests[i]
        test.status = 'running'
        
        await new Promise(resolve => setTimeout(resolve, 500))
        
        try {
          if (this.currentLesson.id === 1) {
            test.actual = 'Hello, World!'
          } else if (this.currentLesson.id === 2) {
            const [a, b] = test.input.split(' ').map(Number)
            test.actual = (a + b).toString()
          } else if (this.currentLesson.id === 3) {
            const n = Number(test.input)
            let factorial = 1
            for (let j = 1; j <= n; j++) {
              factorial *= j
            }
            test.actual = factorial.toString()
          } else if (this.currentLesson.id === 4) {
            const n = Number(test.input)
            test.actual = n % 2 === 0 ? 'Четное' : 'Нечетное'
          } else if (this.currentLesson.id === 5) {
            const [a, b, c] = test.input.split(' ').map(Number)
            test.actual = Math.max(a, b, c).toString()
          } else if (this.currentLesson.id === 6) {
            const s = test.input
            test.actual = s === s.split('').reverse().join('') ? 'Палиндром' : 'Не палиндром'
          } else if (this.currentLesson.id === 7) {
            const n = Number(test.input)
            let a = 0, b = 1
            for (let j = 2; j <= n; j++) {
              [a, b] = [b, a + b]
            }
            test.actual = n === 0 ? '0' : b.toString()
          } else if (this.currentLesson.id === 8) {
            const n = Number(test.input)
            let isPrime = n > 1
            for (let j = 2; j <= Math.sqrt(n); j++) {
              if (n % j === 0) {
                isPrime = false
                break
              }
            }
            test.actual = isPrime ? 'Простое' : 'Составное'
          } else if (this.currentLesson.id === 9) {
            const s = test.input
            test.actual = s.split('').reverse().join('')
          } else if (this.currentLesson.id === 10) {
            const [a, b, c] = test.input.split(' ').map(Number)
            const sorted = [a, b, c].sort((x, y) => x - y)
            test.actual = sorted.join(' ')
          } else {
            throw new Error('Тесты не реализованы для этого урока')
          }
          
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
      
      await this.runTests()
      
      if (this.passedTests === this.currentLesson.tests.length) {
        const lessonIndex = this.lessons.findIndex(l => l.id === this.currentLesson.id)
        if (lessonIndex !== -1) {
          this.lessons[lessonIndex].completed = true
          this.progress = Math.round((this.lessons.filter(l => l.completed).length / this.lessons.length) * 100)
        }
        
        this.consoleOutput += '\n🎉 Поздравляем! Все тесты пройдены!\n'
      } else {
        this.consoleOutput += '\n❌ Не все тесты пройдены. Продолжайте работать!\n'
      }
      
      this.isSubmitting = false
    },
    saveCode() {
      localStorage.setItem(`python_lesson_${this.currentLesson.id}`, this.userCode)
      this.consoleOutput = 'Код сохранен локально.\n'
    },
    clearOutput() {
      this.consoleOutput = ''
    }
  }
}
</script>

<style scoped>
.course-page {
  min-height: 100vh;
  background-color: #0E1117;
  color: #E2E8F0;
  padding: 20px;
}

.course-layout {
  display: grid;
  grid-template-columns: 340px 1fr;
  min-height: calc(100vh - 40px);
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

/* Сайдбар с улучшенным скроллбаром */
.sidebar {
  background: #303030;
  border: 1px solid #404040;
  border-radius: 16px;
  box-shadow: 0 6px 24px rgba(0, 0, 0, 0.4);
  position: sticky;
  top: 20px;
  height: fit-content;
  max-height: calc(100vh - 140px);
  overflow: hidden;
  margin-left: 0;
}

/* Внутренний контейнер с прокруткой */
.sidebar-content {
  height: 100%;
  overflow-y: auto;
  max-height: calc(100vh - 140px);
  padding-right: 8px;
  margin-right: -8px;
}

/* Кастомный скроллбар для внутреннего контейнера */
.sidebar-content::-webkit-scrollbar {
  width: 12px;
}

.sidebar-content::-webkit-scrollbar-track {
  background: #252525;
  border-radius: 8px;
  margin: 10px 0;
  border: 1px solid #404040;
  margin-left: 4px;
}

.sidebar-content::-webkit-scrollbar-thumb {
  background: linear-gradient(135deg, #3B82F6, #8B5CF6);
  border-radius: 8px;
  border: 3px solid #252525;
  min-height: 60px;
}

.sidebar-content::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(135deg, #2563EB, #7C3AED);
}

.sidebar-content {
  scrollbar-width: thin;
  scrollbar-color: #3B82F6 #252525;
}

.sidebar-header {
  padding: 1.25rem;
  border-bottom: 1px solid #404040;
  position: sticky;
  top: 0;
  background: #303030;
  z-index: 10;
  border-radius: 16px 16px 0 0;
}

.sidebar-header h2 {
  margin: 0 0 0.75rem 0;
  color: #F8FAFC;
  font-size: 1.2rem;
  font-weight: 600;
}

.progress-bar {
  background: #404040;
  border-radius: 8px;
  height: 6px;
  margin-bottom: 0.5rem;
  overflow: hidden;
}

.progress-fill {
  background: #3B82F6;
  height: 100%;
  transition: width 0.3s ease;
}

.progress span {
  font-size: 0.8rem;
  color: #94A3B8;
  font-weight: 500;
}

.lessons-list {
  padding: 0.875rem;
  display: flex;
  flex-direction: column;
  gap: 0.625rem;
}

.lesson-item {
  background: rgba(14, 17, 23, 0.75);
  border-radius: 14px;
  padding: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 1px solid rgba(255, 255, 255, 0.1);
  display: flex;
  align-items: flex-start;
  gap: 0.875rem;
  position: relative;
  overflow: hidden;
}

.lesson-item::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 2px;
  background: linear-gradient(90deg, #3B82F6, #8B5CF6);
  transform: scaleX(0);
  transition: transform 0.3s ease;
}

.lesson-item:hover {
  transform: translateY(-1px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.25);
  border-color: rgba(59, 130, 246, 0.3);
}

.lesson-item:hover::before {
  transform: scaleX(1);
}

.lesson-item--active {
  background: rgba(14, 17, 23, 0.9);
  border-color: #3B82F6;
  box-shadow: 0 3px 16px rgba(59, 130, 246, 0.2);
}

.lesson-item--active::before {
  transform: scaleX(1);
}

.lesson-item--completed {
  border-color: #3B82F6;
}

.lesson-item--completed::before {
  background: linear-gradient(90deg, #3B82F6, #8B5CF6);
}

.lesson-icon {
  width: 36px;
  height: 36px;
  border-radius: 10px;
  background: rgba(59, 130, 246, 0.1);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.9rem;
  font-weight: 600;
  flex-shrink: 0;
  transition: all 0.3s ease;
  border: 1px solid rgba(59, 130, 246, 0.2);
  color: #3B82F6;
}

.lesson-item:hover .lesson-icon {
  background: rgba(59, 130, 246, 0.2);
  transform: scale(1.03);
}

.lesson-item--active .lesson-icon {
  background: #3B82F6;
  color: white;
  border-color: #3B82F6;
}

.lesson-item--completed .lesson-icon {
  background: #3B82F6;
  color: white;
  border-color: #3B82F6;
}

.lesson-info {
  flex: 1;
  min-width: 0;
}

.lesson-info h3 {
  margin: 0 0 0.375rem 0;
  font-size: 0.9rem;
  color: #F8FAFC;
  font-weight: 600;
  line-height: 1.3;
}

.lesson-info p {
  margin: 0;
  font-size: 0.8rem;
  color: #94A3B8;
  line-height: 1.4;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  line-clamp: 2;
}

/* Основной контент */
.main-content {
  padding: 0;
  overflow-y: auto;
  max-height: calc(100vh - 40px);
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

.problem-section,
.code-section,
.control-panel,
.output-section,
.tests-section {
  background: #303030;
  border-radius: 16px;
  padding: 1.25rem;
  border: 1px solid #404040;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.35);
  margin: 0;
}

.problem-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1rem;
}

.problem-header h1 {
  margin: 0;
  color: #F8FAFC;
  font-size: 1.4rem;
  font-weight: 600;
}

.difficulty {
  padding: 0.35rem 0.875rem;
  border-radius: 16px;
  font-size: 0.75rem;
  font-weight: 600;
}

.difficulty--easy {
  background: #3B82F6;
  color: white;
}

.difficulty--medium {
  background: #8B5CF6;
  color: white;
}

.difficulty--hard {
  background: #EF4444;
  color: white;
}

.problem-description {
  line-height: 1.6;
  color: #E2E8F0;
  font-size: 0.95rem;
}

.code-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.code-header h3 {
  margin: 0;
  color: #F8FAFC;
  font-size: 1.1rem;
  font-weight: 600;
}

.code-actions {
  display: flex;
  gap: 0.5rem;
}

/* Исправленный редактор кода */
.code-editor-container {
  position: relative;
  background: #1E1E1E;
  border-radius: 12px;
  border: 1px solid #404040;
  height: 400px;
  font-family: 'Monaco', 'Consolas', monospace;
  display: flex;
  flex-direction: column;
}

.code-editor-wrapper {
  position: relative;
  flex: 1;
  display: flex;
}

.simple-editor {
  flex: 1;
  background: transparent;
  border: none;
  color: #E2E8F0;
  padding: 0;
  margin: 0;
  font-family: inherit;
  font-size: 14px;
  line-height: 1.5;
  resize: none;
  outline: none;
  tab-size: 2;
  white-space: pre;
  overflow-wrap: normal;
  overflow: auto;
  width: 100%;
  height: 100%;
  min-height: 0;
  max-height: none;
  box-sizing: border-box;
  padding-left: 3rem;
}

.simple-editor::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

.simple-editor::-webkit-scrollbar-track {
  background: #1E1E1E;
  border-radius: 4px;
}

.simple-editor::-webkit-scrollbar-thumb {
  background: #3B82F6;
  border-radius: 4px;
}

.line-numbers {
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 3rem;
  background: #252526;
  border-right: 1px solid #404040;
  padding: 0.5rem 0;
  font-size: 12px;
  color: #858585;
  text-align: right;
  line-height: 1.5;
  user-select: none;
  font-family: 'Monaco', 'Consolas', monospace;
  z-index: 1;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.line-numbers span {
  padding-right: 0.5rem;
  width: 100%;
}

.control-buttons {
  display: flex;
  gap: 0.75rem;
  flex-wrap: wrap;
}

.btn {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.7rem 1.1rem;
  border: none;
  border-radius: 10px;
  font-weight: 600;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.2s ease;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
  transform: none !important;
}

.btn--primary {
  background: #3B82F6;
  color: white;
}

.btn--primary:hover:not(:disabled) {
  background: #2563EB;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(37, 99, 235, 0.3);
}

.btn--secondary {
  background: #8B5CF6;
  color: white;
}

.btn--secondary:hover:not(:disabled) {
  background: #7C3AED;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(124, 58, 237, 0.3);
}

.btn--success {
  background: #3B82F6;
  color: white;
}

.btn--success:hover:not(:disabled) {
  background: #2563EB;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(37, 99, 235, 0.3);
}

.btn--outline {
  background: transparent;
  color: #94A3B8;
  border: 1px solid #505050;
}

.btn--outline:hover:not(:disabled) {
  background: #404040;
  color: #E2E8F0;
  transform: translateY(-2px);
}

.btn--small {
  padding: 0.45rem 0.9rem;
  font-size: 0.7rem;
  border-radius: 8px;
}

.output-section h3,
.tests-section h3 {
  margin: 0 0 1rem 0;
  color: #F8FAFC;
  font-size: 1.1rem;
  font-weight: 600;
}

.output-header,
.tests-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.console-output {
  background: #1E1E1E;
  border: 1px solid #404040;
  border-radius: 12px;
  padding: 1rem;
  color: #E2E8F0;
  font-family: 'Monaco', 'Consolas', monospace;
  font-size: 0.875rem;
  min-height: 100px;
  max-height: 200px;
  overflow-y: auto;
}

.console-output::-webkit-scrollbar {
  width: 8px;
}

.console-output::-webkit-scrollbar-track {
  background: #1E1E1E;
  border-radius: 4px;
}

.console-output::-webkit-scrollbar-thumb {
  background: #3B82F6;
  border-radius: 4px;
}

.console-output pre {
  margin: 0;
  white-space: pre-wrap;
  word-wrap: break-word;
}

.tests-summary {
  color: #94A3B8;
  font-size: 0.875rem;
}

.tests-passed {
  color: #3B82F6;
  font-weight: 600;
}

.tests-list {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  max-height: 400px;
  overflow-y: auto;
}

.tests-list::-webkit-scrollbar {
  width: 6px;
}

.tests-list::-webkit-scrollbar-track {
  background: #303030;
  border-radius: 3px;
}

.tests-list::-webkit-scrollbar-thumb {
  background: #3B82F6;
  border-radius: 3px;
}

.test-item {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  padding: 1rem;
  background: #1E1E1E;
  border-radius: 12px;
  border: 1px solid #404040;
  transition: all 0.2s ease;
}

.test-item:hover {
  transform: translateY(-1px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}

.test-item--passed {
  border-color: #3B82F6;
  background: rgba(59, 130, 246, 0.05);
}

.test-item--failed {
  border-color: #EF4444;
  background: rgba(239, 68, 68, 0.05);
}

.test-item--running {
  border-color: #F59E0B;
  background: rgba(245, 158, 11, 0.05);
}

.test-icon {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.75rem;
  flex-shrink: 0;
  margin-top: 0.125rem;
}

.test-item--passed .test-icon {
  background: #3B82F6;
  color: white;
}

.test-item--failed .test-icon {
  background: #EF4444;
  color: white;
}

.test-item--running .test-icon {
  background: #F59E0B;
  color: white;
}

.test-content {
  flex: 1;
  font-size: 0.875rem;
}

.test-input,
.test-expected,
.test-actual,
.test-error {
  margin-bottom: 0.25rem;
}

.test-input strong,
.test-expected strong,
.test-actual strong,
.test-error strong {
  color: #F8FAFC;
}

.test-actual {
  color: #3B82F6;
}

.test-error {
  color: #EF4444;
}

@media (max-width: 1024px) {
  .course-layout {
    grid-template-columns: 1fr;
  }
  
  .sidebar {
    display: none;
  }
  
  .main-content {
    margin-top: 0;
    padding: 0;
  }
}

@media (max-width: 768px) {
  .course-page {
    padding: 10px;
  }
  
  .main-content {
    padding: 0;
  }
  
  .control-buttons {
    flex-direction: column;
  }
  
  .btn {
    justify-content: center;
  }
  
  .code-editor-container {
    height: 300px;
  }
  
  .problem-section,
  .code-section,
  .control-panel,
  .output-section,
  .tests-section {
    border-radius: 16px;
  }
}
</style>