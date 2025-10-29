<template>
  <div class="auth-page">
    <main class="main">
      <div class="container">
        <div class="auth-container">
          <div class="auth-card">
            <h1 class="auth-title">CodeForge</h1>
            
            <div class="auth-tabs">
              <button class="auth-tab auth-tab--active">Вход</button>
              <button class="auth-tab">Регистрация</button>
            </div>
            
            <form class="auth-form" @submit.prevent="handleLogin">
              <div class="form-group">
                <label for="email" class="form-label">Email:</label>
                <input 
                  type="email" 
                  id="email" 
                  class="form-input" 
                  placeholder="pochta@"
                  v-model="email"
                  required
                  ref="emailInput"
                >
              </div>
              
              <div class="form-group">
                <label for="password" class="form-label">Пароль:</label>
                <input 
                  type="password" 
                  id="password" 
                  class="form-input" 
                  placeholder="Введите пароль"
                  v-model="password"
                  required
                >
              </div>
              
              <div class="form-options">
                <a href="#" class="forgot-password">Забыли пароль?</a>
              </div>
              
              <button class="auth-submit-btn" type="submit">
                Вход
              </button>
            </form>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'AuthPage',
  data() {
    return {
      email: '',
      password: ''
    }
  },
  methods: {
    handleLogin() {
      // Простая имитация входа
      const userData = {
        isLoggedIn: true,
        username: this.email.split('@')[0] || 'User',
        userAvatar: (this.email.split('@')[0] || 'U').charAt(0).toUpperCase()
      }
      
      // Сохраняем в глобальный store
      this.$store.setUser(userData)
      
      // Сохраняем в localStorage
      localStorage.setItem('user', JSON.stringify(userData))
      
      // Переходим на главную страницу
      this.$router.push('/')
    }
  },
  
  mounted() {
    // Фокусируемся на поле ввода при загрузке
    this.$nextTick(() => {
      this.$refs.emailInput?.focus()
    })
  }
}
</script>

<style scoped>
.auth-page {
  min-height: 100vh;
  background-color: #0E1117;
  padding-top: 2rem;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.main {
  padding: 2rem 0;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: calc(100vh - 2rem);
}

.auth-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  padding: 2rem 0;
}

.auth-card {
  background: #1F2937;
  border: 1px solid #374151;
  border-radius: 12px;
  padding: 3rem;
  width: 100%;
  max-width: 400px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
}

.auth-title {
  color: #E5E7EB;
  font-size: 2rem;
  font-weight: bold;
  text-align: center;
  margin-bottom: 2rem;
  font-family: monospace;
}

.auth-tabs {
  display: flex;
  background: #374151;
  border-radius: 8px;
  padding: 4px;
  margin-bottom: 2rem;
}

.auth-tab {
  flex: 1;
  background: transparent;
  border: none;
  color: #9CA3AF;
  padding: 12px 16px;
  border-radius: 6px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
  font-size: 16px;
}

.auth-tab:hover {
  color: #E5E7EB;
  background: #4B5563;
}

.auth-tab--active {
  color: #E5E7EB;
  background: #3B82F6;
}

.auth-tab:active {
  transform: scale(0.98);
}

.auth-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.form-label {
  color: #E5E7EB;
  font-weight: 500;
  font-size: 16px;
}

.form-input {
  background: #374151;
  border: 1px solid #4B5563;
  border-radius: 8px;
  padding: 12px 16px;
  color: #E5E7EB;
  font-size: 16px;
  transition: all 0.2s;
}

.form-input:focus {
  outline: none;
  border-color: #3B82F6;
  background: #4B5563;
}

.form-input::placeholder {
  color: #9CA3AF;
}

.form-options {
  display: flex;
  justify-content: flex-end;
}

.forgot-password {
  color: #3B82F6;
  text-decoration: none;
  font-size: 14px;
  transition: color 0.2s;
}

.forgot-password:hover {
  color: #60A5FA;
  text-decoration: underline;
}

.auth-submit-btn {
  background: #3B82F6;
  color: #E5E7EB;
  border: none;
  border-radius: 8px;
  padding: 14px 20px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
  margin-top: 1rem;
}

.auth-submit-btn:hover {
  background: #2563EB;
  transform: translateY(-1px);
}

.auth-submit-btn:active {
  transform: scale(0.98);
  background: #1D4ED8;
}

/* Мобильная адаптация */
@media (max-width: 768px) {
  .auth-page {
    padding-top: 1rem;
  }
  
  .main {
    padding: 1rem 0;
    min-height: calc(100vh - 1rem);
  }
  
  .auth-card {
    padding: 2rem;
    margin: 0 1rem;
  }
  
  .auth-title {
    font-size: 1.75rem;
  }
}

@media (max-width: 480px) {
  .auth-card {
    padding: 1.5rem;
  }
  
  .auth-title {
    font-size: 1.5rem;
  }
  
  .auth-tab {
    padding: 10px 12px;
    font-size: 14px;
  }
  
  .form-input {
    padding: 10px 14px;
  }
  
  .auth-submit-btn {
    padding: 12px 16px;
  }
}
</style>