<template>
	<div id="app">
		<div v-if="isLoading">Cargando...</div>
		<!-- El key asegura que Vue detecte el cambio de componente -->
		<transition name="fade" mode="out-in">
			<div>
				<UserLogin v-if="!isAuthenticated && !isLoading" @login-success="handleLoginSuccess" key="login" />
				<LinkShortener v-if="isAuthenticated && !isLoading" msg="Acorta er(pango.) link" key="linkshortener" />
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
				// isAuthenticated: false, // Controla si el usuario ha iniciado sesión
				isAuthenticated: false, // Verificar si el token existe
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
					const response = await fetch(`${this.API_URL}/api/verify-token`, {
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
						// this.logout(); // Si el token es inválido, forzamos logout
						this.isAuthenticated = false;
					}
				} catch (error) {
					console.error('Error verificando el token:', error);
					// this.logout();
					this.isAuthenticated = false;
				} finally {
					this.isLoading = false;
				}
			},
			logout() {
				localStorage.removeItem('authToken');
				this.isAuthenticated = false;
			},
			
			handleLoginSuccess() {
				this.isAuthenticated = true; // Cambia el estado tras el inicio de sesión exitoso
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
		margin-top: 60px;
	}






		/* 🎨 Animación de transición */
	.fade-enter-active, .fade-leave-active {
		transition: opacity 0.5s ease;  /* Duración y suavidad */
	}
	.fade-enter-from, .fade-leave-to {
		opacity: 0;  /* Inicio/fin transparente */
	}
	.fade-enter-to, .fade-leave-from {
		opacity: 1;  /* Componente completamente visible */
	}

	* Estilo del indicador de carga */
	#app div {
		font-size: 1.5rem;
		color: #888;
	}
</style>
	