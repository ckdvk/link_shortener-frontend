<template>
	<div class="login-container">
		<h1>Login</h1>
		<div class="form-container">
			<input 
				v-model="username"
				type="text"
				placeholder="Username"
				@keyup.enter="login"
			/>
			<div class="password-container">
				<input 
					v-model="password" 
					type="password" 
					placeholder="Password"
					class="password-input"
					@keyup.enter="login"
				/>
				<button @click="login" @keyup.enter="login">Login</button>
			</div>
			<p v-if="errorMessage" class="error">{{ errorMessage }}</p>
			<LinkShortener v-if="isAuthenticated" msg="Link Shortener" />
		</div>
	</div>
</template>

<script>
console.log("Datos de entorno")
console.log(process.env.VUE_APP_API_URL)
import LinkShortener from './LinkShortener.vue';

export default {
	name: 'UserLogin',
	components: {
		LinkShortener
	},
	data() {
		return {
			username: '',
			password: '',
			isAuthenticated: false,
			errorMessage: '',
			showPassword: false,
			API_URL: process.env.VUE_APP_API_URL,
		};
	},
	methods: {
		login() {
			// if (this.username === 'user' && this.password === 'password') {
			// 	this.isAuthenticated = true;
			// 	this.$emit('login-success');
			// 	this.errorMessage = '';
			// } else {
			// 	this.errorMessage = 'Invalid username or password';
			// }
			const data = {
				username: this.username,
				password: this.password,
			};
			// Petición al backend para obtener el token
			fetch(`${this.API_URL}/login`, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify(data),
			})
			.then(response => response.json())
			.then(data => {
				if (data.token) {
					localStorage.setItem('authToken', data.token); // Guardar el token
					this.isAuthenticated = true;
					this.$emit('login-success');
					this.errorMessage = '';
				} else {
					this.errorMessage = 'Invalid username or password';
				}
			})
			.catch(error => {
				console.error('Error:', error);
				this.errorMessage = 'An error occurred during login';
			});
		},
		togglePassword() {
			this.showPassword = !this.showPassword;
		},
	}
};
</script>

<style scoped>
.login-container {
	max-width: 300px;
	margin: 50px auto;
	background: transparent;
	text-align: center;
}

.form-container {
	max-width: 16em;
	margin: 0 auto;
	text-align: left;
}

.password-container {
	display: flex;
	align-items: center;
	justify-content: space-between;
}

input {
	border: none;
	outline: none;
	padding: 8px;
	font-size: 16px;
	width: 100%;
	box-sizing: border-box;
	color:#26704f;
	font-family: 'Courier New', Courier, monospace;
	/* input box is transparent */
	background-color: transparent;
}
input::placeholder {
		color: rgb(194, 24, 123);  /* Puedes cambiar 'purple' por cualquier color que prefieras */
		text-align: left;
	}

.password-input {
	flex: 1;
	margin-right: 8px;
}

button {
	/* background-color: #acda5736; */
	background-color: transparent;
	border-radius: 10%;
	padding: 0.7em;
	cursor: pointer;
	border: none;
	box-shadow: none;
	transition: box-shadow 0.3s ease;
	font-family: 'Courier New', Courier, monospace;
	font-size: 14px;
}

button:hover {
	/* box-shadow: 0 0 5px 2px rgba(177, 196, 94, 0.3); */
	color: rgb(194, 24, 123);
}

.error {
	color: rgb(168, 5, 5);
	margin-top: 10px;
}
</style>
