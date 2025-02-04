<template>
	<div class="hello">
		<!-- Al pulsar refresca la página -->
		<h1 @click="refreshPage" style="cursor: pointer;">{{ msg }}</h1>
		<!-- A beautiful text input for the link to shorten-->
		<div style="max-width: 15em; margin: 0 auto;">
			<input 
				v-model="link" 
				type="text"
				placeholder="Enter your link here"
				style="text-align: left;"
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
		<div v-if="showToast" class="toast">
			<p>{{ toastMessage }}</p>
		</div>
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
				errorFound: false // Variable booleana para controlar la visibilidad del mensaje de error
			};
		},
		methods: {
			// Método para refrescar la página
			refreshPage() {
				window.location.reload();
			},
			// Método para simular la acción de acortar el enlace
			shortenLink() {
				// Aquí podrías agregar la lógica real para acortar el enlace
				if (this.link) {
					console.log("El usuario ha introducido el enlace: " + this.link);
					// Simulación de enlace acortado
					this.link = "http://localhost:8080/shortened-link"; // Se reemplaza el enlace largo por el acortado
					this.isShortened = true; // Activa el flag cuando el enlace es acortado
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
						this.showToast = false; // Cambia la visibilidad a falsa después de 5 segundos
					}, 3000); // 5000 ms = 5 segundos
				}).catch((err) => {
					console.error("Error al copiar al portapapeles: ", err);
					this.errorFound = true; // Activa el flag de error
					this.toastMessage = "Error copying link to clipboard";
					this.showToast = true; // Muestra la notificación

					// Desaparece el toast después de 5 segundos
					setTimeout(() => {
						this.showToast = false; // Cambia la visibilidad a falsa después de 5 segundos
					}, 3000); // 5000 ms = 5 segundos
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

	}
	input::placeholder {
		color: rgb(194, 24, 123);  /* Puedes cambiar 'purple' por cualquier color que prefieras */
	}
	.disabled-input {
		/* Evitar cambios visuales al deshabilitar el input */
		background-color: white; /* Fondo blanco */
		color: #42b983; /* Texto negro */
	}

	.toast {
		position: fixed;
		bottom: 20px;
		left: 50%;
		transform: translateX(-50%);
		background-color: #333;
		color: white;
		padding: 10px 20px;
		border-radius: 5px;
		box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
		transition: opacity 2s ease-in-out;
		opacity: 0.7;
	}

	/* A modern looking button with very soft borders and beautiful green colors not too much opacity */
	button {
		background-color: #acda571c;
		border-radius: 10%;
		padding: 0.7em;
		cursor: pointer;
		/* Sin borde inicial */
		border: none;
		/* Sin sombra inicial */
		box-shadow: none;
		transition: box-shadow 0.3s ease;  /* Transición suave para el borde */
	}

	/* Aparece el borde al hacer hover */
	button:hover {
		box-shadow: 0 0 5px 2px rgba(177, 196, 94, 0.3);  /* Sombra que simula un borde */
	}
</style>
