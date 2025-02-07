<template>
	<div class="shorten">
		<!-- Al pulsar refresca la página -->
		<h1 @click="refreshPage" style="cursor: pointer;">{{ msg }}</h1>
		<!-- A beautiful text input for the link to shorten-->
		<div style="max-width: 17em; margin: 0 auto;">
			<input 
				v-model="link" 
				type="text"
				placeholder="Enter the link to shorten"
				@keyup.enter="shortenLink"
				:class="{'disabled-input': isShortened}"
				:disabled="isShortened"
			>
		</div>
		<!-- A vertical space here -->
		<div>
			<br>
		</div>
		<!-- A button to shorten the link and, next to it, a Copy button (not activated until the link is shortened)-->
		<div>
			<button @click="shortenLink" v-if="!isShortened">Shorten</button>
			<button v-if="isShortened" @click="copyToClipboard">Copy</button>
		</div>
		<!-- A vertical space here -->
		<div>
			<br>
		</div>
		<!-- Toast Notification -->
		<transition name="fade">
			<div v-if="showToast" class="toast">
				<p>{{ toastMessage }}</p>
			</div>
		</transition>
	</div>
</template>

<script>
	export default {
		name: 'LinkShortener',
		props: {
			msg: String
		},
		data() {
			return {
				link: '', // Variable para almacenar el enlace ingresado o acortado
				isShortened: false, // Variable booleana para controlar la visibilidad
				showToast: false, // Variable booleana para controlar la visibilidad de la notificación
				toastMessage: "Link copied to clipboard!", // Mensaje de la notificación
				toastOpacity: 0.8, // Opacidad de la notificación
				toastDuration: 2000, // Duración de la notificación
				errorFound: false, // Variable booleana para controlar la visibilidad del mensaje de error
				API_URL: process.env.VUE_APP_API_URL,
			};
		},
		methods: {
			// Método para refrescar la página
			refreshPage() {
				this.link = '';
				this.isShortened = false;
				this.showToast = false;
				this.toastMessage = "Link copied to clipboard!";
				this.errorFound = false;
			},
			// Método para simular la acción de acortar el enlace
			shortenLink() {
				// console.log(`URL DE LA API: ${process.env.VUE_APP_API_URL}`); // Esto debería mostrar la URL base que configuraste

				// // Aquí podrías agregar la lógica real para acortar el enlace
				// if (this.link) {
				// 	console.log("El usuario ha introducido el enlace: " + this.link);
				// 	// Simulación de enlace acortado
				// 	this.link = "http://localhost:8080/shortened-link"; // Se reemplaza el enlace largo por el acortado
				// 	this.isShortened = true; // Activa el flag cuando el enlace es acortado
				// }
				if (this.link) {
					const token = localStorage.getItem('authToken'); // Obtener el token almacenado

					if (!token) {
						console.error("No token found. Please log in first.");
						this.errorFound = true;
						this.toastMessage = "You must be logged in to shorten links.";
						this.showToast = true;
						return;
					}

					fetch(`${this.API_URL}/shortenUrl`, {
						method: 'POST',
						headers: {
							'Content-Type': 'application/json',
							'Authorization': `Bearer ${token}`,  // Incluir el token en la cabecera
						},
						body: JSON.stringify({ original_url: this.link })
					})
					.then(response => response.json())
					.then(data => {
						if (data.short_url) {
							this.link = data.short_url;
							console.log("Link shortened:", this.link);
							this.isShortened = true;
						} else {
							console.error("Error shortening link:", data);
							this.errorFound = true;
							this.toastMessage = "Error shortening link.";
							this.showToast = true;
							setTimeout(() => {
								this.showToast = false; // Oculta la notificación
							}, this.toastDuration);
						}
					})
					.catch(error => {
						console.error('Error:', error);
						this.errorFound = true;
						this.toastMessage = "Error shortening link.";
						this.showToast = true;
						setTimeout(() => {
								this.showToast = false; // Oculta la notificación
						}, this.toastDuration);
					});
				}
			},
			// Método para copiar el enlace al portapapeles
			copyToClipboard() {
				// Usamos la API Clipboard para copiar el texto
				navigator.clipboard.writeText(this.link).then(() => {
					console.log("Enlace copiado al portapapeles");
					this.showToast = true; // Muestra la notificación
					// Desaparece el toast después de 5 segundos
					setTimeout(() => {
						this.showToast = false; // Oculta la notificación
					}, this.toastDuration);					
				}).catch((err) => {
					console.error("Error al copiar al portapapeles: ", err);
					this.errorFound = true; // Activa el flag de error
					this.toastMessage = "Error copying link to clipboard";
					this.showToast = true; // Muestra la notificación

					// Desaparece el toast después de 5 segundos
					setTimeout(() => {
						this.showToast = false; // Oculta la notificación
					}, this.toastDuration);
				});
			}
		}
	}
</script>

<style scoped>
	h3 {
		margin: 40px 0 0;
	}
	ul {
		list-style-type: none;
		padding: 0;
	}
	li {
		display: inline-block;
		margin: 0 10px;
	}
	a {
		color: #42b983;
	}
	/* Eliminar el borde del input */
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
		text-align: left;
	}
	.disabled-input {
		/* Evitar cambios visuales al deshabilitar el input, además, el texto se ve centrado ahora */
		background-color: transparent; /* Fondo transparente */
		color: #42b983; /*Texto verde */
		text-align: center;
	}
	input::placeholder {
		color: rgb(194, 24, 123);  /* Puedes cambiar 'purple' por cualquier color que prefieras */
	}



	.toast {
		position: fixed;
		bottom: 20px;
		left: 50%;
		transform: translateX(-50%);
		background-color: #acda571c;
		padding: 10px 20px;
		border-radius: 5px;
		box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
		transition: opacity 2.4s ease-out;
		font-family: 'Courier New', Courier, monospace;
	}
	.fade-enter-active,
	.fade-leave-active {
		transition: opacity 0.5s ease-in-out;
	}
	.fade-enter-from,
	.fade-leave-to {
		opacity: 0; /* Empieza y termina con opacidad 0 */
	}
	.fade-enter-to,
	.fade-leave-from {
		opacity: 0.7; /* Totalmente visible */
	}
	/* A modern looking button with very soft borders and beautiful green colors not too much opacity */
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
</style>
