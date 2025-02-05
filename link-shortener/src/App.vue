<template>
	<div id="app">
		<transition name="fade" mode="out-in">
			<!-- El key asegura que Vue detecte el cambio de componente -->
			<UserLogin v-if="!isAuthenticated" @login-success="handleLoginSuccess" key="login" />
			<LinkShortener v-else msg="Acorta er(pango.) link" key="linkshortener" />
		</transition>
		<!-- <UserLogin v-if="!isAuthenticated" @login-success="handleLoginSuccess" />
		<LinkShortener v-else msg="Acorta er(pango.) link" /> -->
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
			isAuthenticated: false // Controla si el usuario ha iniciado sesión
			};
		},
		methods: {
			handleLoginSuccess() {
				this.isAuthenticated = true; // Cambia el estado tras el inicio de sesión exitoso
			},
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
</style>
	