<template>
  <CourseLayout
    course-title="Курс Python"
    :lessons="pythonLessons"
    language="python"
  />
</template>

<script>
import CourseLayout from './CourseLayout.vue'

export default {
  name: 'Python',
  components: {
    CourseLayout
  },
  data() {
    return {
      pythonLessons: [
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
      ]
    }
  }
}
</script>