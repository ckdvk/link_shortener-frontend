<template>
	<div class="shorten">
		<h1 @click="refreshPage" style="cursor: pointer;">{{ msg }}</h1>
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
		<div>
			<br>
		</div>
		<div>
			<button @click="shortenLink" v-if="!isShortened">Shorten</button>
			<button v-if="isShortened" @click="copyToClipboard">Copy</button>
		</div>
		<div>
			<br>
		</div>
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
				link: '',
				isShortened: false,
				showToast: false,
				toastMessage: "Link copied to clipboard!",
				toastOpacity: 0.8,
				toastDuration: 2000,
				errorFound: false,
				API_URL: process.env.VUE_APP_API_URL,
			};
		},
		methods: {
			refreshPage() {
				this.link = '';
				this.isShortened = false;
				this.showToast = false;
				this.toastMessage = "Link copied to clipboard!";
				this.errorFound = false;
			},
			
			shortenLink() {
				if (this.link) {
					const token = localStorage.getItem('authToken');

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
							'Authorization': `Bearer ${token}`,
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
								this.showToast = false;
							}, this.toastDuration);
						}
					})
					.catch(error => {
						console.error('Error:', error);
						this.errorFound = true;
						this.toastMessage = "Error shortening link.";
						this.showToast = true;
						setTimeout(() => {
								this.showToast = false;
						}, this.toastDuration);
					});
				}
			},
			copyToClipboard() {
				navigator.clipboard.writeText(this.link).then(() => {
					console.log("Enlace copiado al portapapeles");
					this.showToast = true;
					setTimeout(() => {
						this.showToast = false;
					}, this.toastDuration);
				}).catch((err) => {
					console.error("Error al copiar al portapapeles: ", err);
					this.errorFound = true;
					this.toastMessage = "Error copying link to clipboard";
					this.showToast = true;

					setTimeout(() => {
						this.showToast = false;
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
		background-color: transparent;
		color: #42b983;
		text-align: center;
	}
	input::placeholder {
		color: rgb(194, 24, 123);
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
		opacity: 0;
	}
	.fade-enter-to,
	.fade-leave-from {
		opacity: 0.7;
	}
	button {
		background-color: #acda5736;
		border-radius: 10%;
		padding: 0.7em;
		cursor: pointer;
		border: none;
		box-shadow: none;
		transition: box-shadow 0.3s ease;
		font-family: 'Courier New', Courier, monospace;
	}
	button:hover {
		box-shadow: 0 0 5px 2px rgba(177, 196, 94, 0.3);
	}
</style>
