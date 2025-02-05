<template>
	<div class="login-container">
		<h1>Login</h1>
		<!-- the content of this div is centered in the screen. Not only the text -->
		<div style="max-width: 16em; margin: 0 auto; text-align: center;">
			<input 
				v-model="username"
				type="text"
				placeholder="Username"
				@keyup.enter="login"
			/>
			<!-- <input 
				v-model="password" 
				:type="showPassword ? 'text' : 'password'" 
				placeholder="Password" 
				class="password-input"
				/> -->
			<input 
				v-model="password" 
				type="text" 
				placeholder="Password"
				class = "password-input"
				@keyup.enter="login"
			/>
			<div>
				<br>
			</div>
			<button @click="login" @keyup.enter="login">Login</button>
			<div>
				<br>
			</div>
		
			<LinkShortener v-if="isAuthenticated" msg="Link Shortener" />
		</div>
		<p v-if="errorMessage" class="error">{{ errorMessage }}</p>

	</div>
</template>
	
	<script>
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
			};
		},
		methods: {
			login() {
			// Validación simple para ejemplo
			if (this.username === 'user' && this.password === 'password') {
				this.isAuthenticated = true;
				this.$emit('login-success');
				this.errorMessage = '';
			} else {
				this.errorMessage = 'Invalid username or password';
			}
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
		/* padding: 20px; */
		background: transparent;
		text-align: center;
	}
	/* input {
		display: block;
		width: 90%;
		margin: 10px auto;
		padding: 8px;
		border: 1px solid #ccc;
		border-radius: 5px;
	} */

	button {
		background-color: #acda5736;
		border-radius: 10%;
		padding: 0.7em;
		cursor: pointer;
		/* Sin borde inicial */
		border: none;
		/* Sin sombra inicial */
		box-shadow: none;
		transition: box-shadow 0.3s ease;  /* Transición suave para el borde */
		font-family: 'Courier New', Courier, monospace;
	}
	/* Aparece el borde al hacer hover */
	button:hover {
		box-shadow: 0 0 5px 2px rgba(177, 196, 94, 0.3);  /* Sombra que simula un borde */
	}
	.error {
		color: rgb(168, 5, 5);
		margin-top: 10px;
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
		text-align: center;
	}
	input::placeholder {
		color: rgb(194, 24, 123);  /* Puedes cambiar 'purple' por cualquier color que prefieras */
		text-align: center;
	}

	.password-input {
		border: none;
		outline: none;
		font-size: 16px;
		width: 100%;
		box-sizing: border-box;
		color: #26704f;
		font-family: 'Courier New', Courier, monospace;
		background-color: transparent;
		/* text-align: center; */

		/* Oculta los caracteres */
		-webkit-text-security: disc;  /* Otras opciones: circle, square */
	}

	</style>
	