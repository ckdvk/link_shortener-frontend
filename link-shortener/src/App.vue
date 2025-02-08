<template>
	<div id="app">
		<div v-if="isLoading">Cargando...</div>
		<transition name="fade" mode="out-in">
			<div>
				<UserLogin v-if="!isAuthenticated && !isLoading" @login-success="handleLoginSuccess" key="login" />
				<LinkShortener v-if="isAuthenticated && !isLoading" msg="Shorten your link" key="linkshortener" />
			</div>
		</transition>
	</div>
</template>

<script>
	import UserLogin from './components/UserLogin.vue';
	import LinkShortener from './components/LinkShortener.vue';
	
	export default {
		name: 'App',
		components: {
			UserLogin,
			LinkShortener
		},
		data() {
			return {
				isAuthenticated: false,
				isLoading: true,
				API_URL: process.env.VUE_APP_API_URL,
			};
		},
		methods: {
			async checkTokenValidity() {
				const token = localStorage.getItem('authToken');
				if (!token) {
					console.log('No token found');
					this.isAuthenticated = false;
					this.isLoading = false;
					return;
				}
				try {
					const response = await fetch(`${this.API_URL}/api/tokenRoutes/verify-token`, {
						method: 'POST',
						headers: {
							'Content-Type': 'application/json',
							'Authorization': `Bearer ${token}`
						}
					});

					if (response.ok) {
						console.log('Token válido');
						this.isAuthenticated = true;
					} else {
						console.error('Token inválido');
						this.isAuthenticated = false;
					}
				} catch (error) {
					console.error('Error verificando el token:', error);
					this.isAuthenticated = false;
				} finally {
					this.isLoading = false;
				}
			},
			handleLoginSuccess() {
				this.isAuthenticated = true;
			},
		},
		mounted() {
			console.log('App mounted');
			this.checkTokenValidity();
		}
	};
</script>
	
<style>
	#app {
		font-family: 'Courier New', Courier, monospace;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		text-align: center;
		color: #2c3e50;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 80vh;
	}

	.fade-enter-active, .fade-leave-active {
		transition: opacity 0.5s ease;
	}
	.fade-enter-from, .fade-leave-to {
		opacity: 0;
	}
	.fade-enter-to, .fade-leave-from {
		opacity: 1;
	}

	* Estilo del indicador de carga */
	#app div {
		font-size: 1.5rem;
		color: #888;
	}
</style>
	