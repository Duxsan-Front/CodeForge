<template>
  <CourseLayout
    course-title="Курс JavaScript"
    :lessons="jsLessons"
    language="javascript"
  />
</template>

<script>
import CourseLayout from './CourseLayout.vue'

export default {
  name: 'JavaScript',
  components: {
    CourseLayout
  },
  data() {
    return {
      jsLessons: [
        {
          id: 1,
          title: 'Привет, JavaScript!',
          description: 'Напишите программу, которая выводит "Hello, World!" на экран.',
          difficulty: 'easy',
          completed: true,
          tests: [
            { input: '', expected: 'Hello, World!', actual: null, status: null, error: null }
          ],
          starterCode: 'console.log("Hello, World!")'
        },
        {
          id: 2,
          title: 'Сумма двух чисел',
          description: 'Напишите программу, которая принимает два числа и выводит их сумму.',
          difficulty: 'easy',
          completed: false,
          tests: [
            { input: '5 3', expected: '8', actual: null, status: null, error: null },
            { input: '10 20', expected: '30', actual: null, status: null, error: null }
          ],
          starterCode: '// Введите ваш код здесь\nconst num1 = parseInt(prompt());\nconst num2 = parseInt(prompt());\nconsole.log(num1 + num2);'
        },
        // ... остальные уроки JavaScript
      ]
    }
  }
}
</script>