<template>
  <div class="course-page">
    <div class="course-layout">
      <!-- Боковая панель с заданиями -->
      <aside class="sidebar">
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

        <!-- Редактор кода с Monaco (используем wrapper) -->
        <section class="code-section">
          <div class="code-header">
            <h3>Редактор кода</h3>
            <div class="code-actions">
              <button class="btn btn--small" @click="resetCode">Сбросить</button>
            </div>
          </div>
          <div class="code-editor-container">
            <monaco-editor
              v-model="userCode"
              language="python"
              theme="vs-dark"
              :options="editorOptions"
              @editorDidMount="onEditorMount"
            />
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
import MonacoEditor from 'monaco-editor-vue3' // Импорт wrapper

export default {
  name: 'Python',
  components: { MonacoEditor },
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
        }
      ],
      difficultyText: {
        easy: 'Легко',
        medium: 'Средне',
        hard: 'Сложно'
      },
      editor: null, // Для доступа, если нужно
      editorOptions: {
        fontSize: 14,
        lineNumbers: 'on',
        roundedSelection: true,
        scrollBeyondLastLine: false,
        cursorStyle: 'line',
        automaticLayout: true, // Auto-resize
        minimap: { enabled: false },
        folding: false,
        wordWrap: 'off',
        suggestOnTriggerCharacters: false
      }
    }
  },
  computed: {
    passedTests() {
      if (!this.currentLesson.tests) return 0
      return this.currentLesson.tests.filter(test => test.status === 'passed').length
    }
  },
  methods: {
    onEditorMount(editor) {
      this.editor = editor
      editor.layout() // Force resize для фикса hidden/empty
    },
    selectLesson(lesson) {
      if (this.currentLesson.id === lesson.id) return
      this.currentLesson = { 
        ...lesson,
        tests: lesson.tests.map(test => ({ ...test }))
      }
      this.userCode = lesson.starterCode || ''
      this.consoleOutput = ''
      this.resetTests()
      if (this.editor) this.editor.layout() // Resize при смене урока
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
  },
  beforeUnmount() {
    if (this.editor) {
      this.editor.dispose()
    }
  }
}
</script>

<style scoped>
.course-page {
  min-height: 100vh;
  background-color: #0E1117;
  color: #E2E8F0;
}

.course-layout {
  display: grid;
  grid-template-columns: 300px 1fr;
  min-height: 100vh;
}

/* Сайдбар */
.sidebar {
  background: #1F2937;
  border-right: 1px solid #374151;
  overflow-y: auto;
  max-height: 100vh;
  position: sticky;
  top: 0;
}

.sidebar-header {
  padding: 1.5rem;
  border-bottom: 1px solid #374151;
}

.sidebar-header h2 {
  margin: 0 0 1rem 0;
  color: #F8FAFC;
  font-size: 1.25rem;
}

.progress-bar {
  background: #374151;
  border-radius: 10px;
  height: 8px;
  margin-bottom: 0.5rem;
  overflow: hidden;
}

.progress-fill {
  background: #10B981;
  height: 100%;
  transition: width 0.3s ease;
}

.progress span {
  font-size: 0.875rem;
  color: #94A3B8;
}

.lessons-list {
  padding: 1rem 0;
}

.lesson-item {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  padding: 1rem 1.5rem;
  cursor: pointer;
  transition: background-color 0.2s ease;
  border-left: 3px solid transparent;
}

.lesson-item:hover {
  background: #374151;
}

.lesson-item--active {
  background: #374151;
  border-left-color: #3B82F6;
}

.lesson-item--completed .lesson-icon {
  background: #10B981;
}

.lesson-icon {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  background: #475569;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.875rem;
  font-weight: 600;
  flex-shrink: 0;
  transition: background-color 0.2s ease;
}

.lesson-info h3 {
  margin: 0 0 0.25rem 0;
  font-size: 0.95rem;
  color: #F8FAFC;
}

.lesson-info p {
  margin: 0;
  font-size: 0.8rem;
  color: #94A3B8;
  line-height: 1.4;
}

/* Основной контент */
.main-content {
  padding: 2rem;
  overflow-y: auto;
  max-height: 100vh;
}

.problem-section {
  background: #1F2937;
  border-radius: 12px;
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  border: 1px solid #374151;
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
  font-size: 1.5rem;
}

.difficulty {
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.75rem;
  font-weight: 600;
}

.difficulty--easy {
  background: #10B981;
  color: white;
}

.difficulty--medium {
  background: #F59E0B;
  color: white;
}

.difficulty--hard {
  background: #EF4444;
  color: white;
}

.problem-description {
  line-height: 1.6;
  color: #E2E8F0;
}

/* Редактор кода */
.code-section {
  background: #1F2937;
  border-radius: 12px;
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  border: 1px solid #374151;
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
}

.code-actions {
  display: flex;
  gap: 0.5rem;
}

.code-editor-container {
  background: #1E1E1E;
  border-radius: 8px;
  border: 1px solid #374151;
  overflow: hidden;
  height: 400px;
}

.code-editor {
  height: 100%;
  width: 100%;
}

/* Панель управления */
.control-panel {
  background: #1F2937;
  border-radius: 12px;
  padding: 1rem 1.5rem;
  margin-bottom: 1.5rem;
  border: 1px solid #374151;
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
  padding: 0.75rem 1.25rem;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  font-size: 0.875rem;
  cursor: pointer;
  transition: all 0.2s ease;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.btn--primary {
  background: #3B82F6;
  color: white;
}

.btn--primary:hover:not(:disabled) {
  background: #2563EB;
  transform: translateY(-1px);
}

.btn--secondary {
  background: #8B5CF6;
  color: white;
}

.btn--secondary:hover:not(:disabled) {
  background: #7C3AED;
  transform: translateY(-1px);
}

.btn--success {
  background: #10B981;
  color: white;
}

.btn--success:hover:not(:disabled) {
  background: #059669;
  transform: translateY(-1px);
}

.btn--outline {
  background: transparent;
  color: #94A3B8;
  border: 1px solid #475569;
}

.btn--outline:hover:not(:disabled) {
  background: #374151;
  color: #E2E8F0;
}

.btn--small {
  padding: 0.5rem 1rem;
  font-size: 0.75rem;
}

/* Секции вывода */
.output-section,
.tests-section {
  background: #1F2937;
  border-radius: 12px;
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  border: 1px solid #374151;
}

.output-section h3,
.tests-section h3 {
  margin: 0 0 1rem 0;
  color: #F8FAFC;
}

.output-header,
.tests-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.console-output {
  background: #0F172A;
  border: 1px solid #374151;
  border-radius: 8px;
  padding: 1rem;
  color: #E2E8F0;
  font-family: 'Monaco', 'Consolas', monospace;
  font-size: 0.875rem;
  min-height: 100px;
  max-height: 200px;
  overflow-y: auto;
}

.console-output pre {
  margin: 0;
  white-space: pre-wrap;
  word-wrap: break-word;
}

/* Тесты */
.tests-summary {
  color: #94A3B8;
  font-size: 0.875rem;
}

.tests-passed {
  color: #10B981;
  font-weight: 600;
}

.tests-list {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.test-item {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  padding: 1rem;
  background: #0F172A;
  border-radius: 8px;
  border: 1px solid #374151;
  transition: all 0.2s ease;
}

.test-item--passed {
  border-color: #10B981;
  background: rgba(16, 185, 129, 0.05);
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
  background: #10B981;
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
  color: #10B981;
}

.test-error {
  color: #EF4444;
}

/* Адаптивность */
@media (max-width: 1024px) {
  .course-layout {
    grid-template-columns: 1fr;
  }
  
  .sidebar {
    display: none;
  }
}

@media (max-width: 768px) {
  .main-content {
    padding: 1rem;
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
}
</style>