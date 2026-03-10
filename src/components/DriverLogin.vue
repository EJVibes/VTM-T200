<template>
    <div class="login-container">
        <div class="login-form">
            <h2 id="login-title">Driver ID</h2>
            <input :value="step === 1 ? userId : code" @input="onInput($event.target.value)"
                :placeholder="step === 1 ? 'Enter User ID' : 'Enter Login Code'" class="username-input" />
            <div class="button-grid">
                <button class="buttonKey" v-for="button in buttons" :key="button" @click="handleButtonClick(button)">
                    {{ button }}
                </button>
                <a href="https://www.mybustimes.cc/u/TicketerCode" target="_blank">
                    Get Login Code
                </a>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'DriverLogin',
    data() {
        return {
            step: 1, // 1: entering ID, 2: entering code
            userId: '',
            code: '',
            buttons: ['1', '2', '3', '4', '5', '6', '7', '8', '9', 'C', '0', 'OK'],
        };
    },
    methods: {
        onInput(value) {
            if (this.step === 1) {
                this.userId = value;
            } else {
                this.code = value;
            }
        },
        playSound(name) {
            const audio = new Audio(require(`@/assets/Audio/${name}.wav`));
            audio.play();
        },
        async handleButtonClick(button) {
            if (button === 'C') {
                if (this.step === 1) this.userId = '';
                else this.code = '';
                return;
            }

            if (button === 'OK') {
                if (this.step === 1) {
                    console.log('User ID:', this.userId);
                    document.getElementById('login-title').innerText = 'Pass Code';
                    if (this.userId.trim()) {
                        this.step = 2;
                    } else {
                        alert('Please enter your User ID');
                    }
                } else if (this.step === 2) {
                    if (this.code.trim()) {
                        try {
                            const response = await axios.post('https://www.mybustimes.cc/api/user/', {
                                user_id: this.userId,
                                code: this.code,
                            }, {
                                headers: { 'Content-Type': 'application/json' }
                            });

                            console.log(response);

                            let session_key = response.data.session_key;

                            const username = response.data.username || 'Unknown';
                            localStorage.setItem('username', username);
                            localStorage.setItem('user_id', this.userId);
                            localStorage.setItem('session_key', session_key)
                            this.redirect();

                        } catch (error) {
                            console.error('Login failed:', error);
                            this.playSound('Ticket decline');
                            alert('Invalid login. Please try again.');
                            this.code = '';
                            this.step = 1; // back to ID entry
                        }
                    } else {
                        alert('Please enter your login code');
                    }
                }
            } else {
                if (this.step === 1) {
                    this.userId += button;
                } else {
                    this.code += button;
                }
            }
        },
        redirect() {
            this.playSound('Logon');
            setTimeout(() => {
                this.$router.push({ path: '/LoadUserData' });
            }, 1500);
        },
    },
};
</script>

<style scoped>
.login-container {
    position: fixed;
    top: 10vh;
    left: 10vw;
    width: 80vw;
    display: flex;
    justify-content: center;
    align-items: center;
}

.login-form {
    width: 100%;
    max-width: 500px;
    height: 60vh;
    padding: 20px;
    border-radius: 8px;
    text-align: center;
}

.username-input {
    width: calc(100% - 25px);
    padding: 10px;
    margin-bottom: 20px;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 16px;
}

.button-grid {
    height: 60vh;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    max-width: 500px;
}

.buttonKey {
    text-align: center;
    font-size: 16px;
    background-color: #004ab9;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.buttonKey:hover {
    background-color: #003c97;
}
</style>
