<template>
	
	<div class="container-fluid bg-light py-5" id="contact">
			      <h2 class="text-center mb-5">Contact</h2>
			      <div class="row justify-content-center align-items-center">
					<div class="col-md-6 mb-4">
			          <iframe 
			            src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3860.4100410550705!2d121.0411029757416!3d14.63265027628985!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3397b7f5db7f5e3f%3A0xe15d2d6a4453ed00!2sZuitt%20QC!5e0!3m2!1sen!2sph!4v1759134365240!5m2!1sen!2sph" class="map-frame" allowfullscreen="" loading="lazy">
			          </iframe>
			        </div>

			        
			        <div class="col-md-6">
			          <form class="p-4 border rounded bg-white shadow-sm" @submit.prevent="submitForm">
			            <div class="mb-3">
			              <input type="text" class="form-control" id="name" placeholder="First Name M.I. Last Name" v-model="name" required>
			            </div>
			            <div class="mb-3">
			              <input type="email" class="form-control" id="email" placeholder="Email" v-model="email" required autocomplete="off">
			            </div>
			            <div class="mb-3">
			              <textarea class="form-control" id="message" rows="5" placeholder="Message" v-model= "message" required autocomplete="off"></textarea>
			            </div>
			            <div class="d-flex justify-content-between align-items-center">
			            	<div class="social-links">
			            	<a href="https://www.linkedin.com/in/rydel-ann-laforteza-3889b9384/" target="_blank"><img src="/images/LinkedIn.png"></a>
			            	<a href="https://gitlab.com/users/sign_in/?utm_medium=cpc&utm_source=google&utm_campaign=eg_global_dmp_x_x_en_sitelinks&utm_content=sitelinks_sign-in_x_x&&utm_term=gitlab&_bt=656315922370&_bk=gitlab&_bm=e&_bn=g&_bg=148481441276&gad_source=1&gad_campaignid=20029282011&gbraid=0AAAAADcJCbeafD6B_BX1JnslIhCQu0BuA&gclid=CjwKCAjw_-3GBhAYEiwAjh9fUAgG4Et1MB7DW6wi0Pka3YIDjY5A_WRlNFMx7IpfyXPBc3P3EK-PAhoCJTsQAvD_BwE" target="_blank"><img src="/images/gitlab.jpg"></a>
			            	<a href="https://github.com/login" target="_blank"><img src="/images/github.png"></a>
			            	</div>

			            <button type="submit" class="btn btn-primary rounded-pill px-4" :disabled="isLoading">{{isLoading ? "Sending..." : "Submit"}}</button>
			            </div>
			            <div class="d-flex justify-content-end mt-2 ">
 							<div ref="recaptchaContainer"></div>
 						</div>
			          </form>
			        </div>

			      </div>
			    </div>

</template>

<script setup>
    
    import { ref, onMounted, onBeforeUnmount } from 'vue';
    import { Notyf } from 'notyf';
    import 'notyf/notyf.min.css';

    const notyf = new Notyf();

    const WEB3FORMS_ACCESS_KEY = "a3460546-71da-4324-bf04-7741feb2a69f";

    const subject = "New message from Portfolio Contact Form";

    const name = ref("");
    const email = ref("");
    const message = ref("");

    const isLoading = ref(false);

    const submitForm = async() => {

        if(!recaptchaToken.value){
            notyf.error("Please verify that you are not a robot.");
            return;
        }

        isLoading.value = true;

        try {
            const response = await fetch("https://api.web3forms.com/submit", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    Accept: "application/json",
                },
                body: JSON.stringify({
                    access_key: WEB3FORMS_ACCESS_KEY,
                    subject: subject,
                    name: name.value,
                    email: email.value,
                    message: message.value
                })
            })
            const result = await response.json();

            if( result.success){
                console.log(result);
                isLoading.value = false;
                notyf.success("Message Sent!");
            }
        } catch(error){
            console.log(error);
            isLoading.value = false;
            notyf.error("Failed to send message")
        } finally {
            resetRecaptcha();
        }
    }

    const SITE_KEY = '6LfqAAosAAAAAIo7qTUgNNmQD6cIOGhdX0vibX_G';

    const recaptchaContainer = ref(null);
    const recaptchaWidgetId = ref(null);
    const recaptchaToken = ref("");

    function onRecaptchaSuccess(token){
        recaptchaToken.value = token;
    }

    function onRecaptchaExpired(){
        recaptchaToken.value = '';
    }

    function renderRecaptcha(){
        if(!window.grecaptcha){
            console.error("recaptcha not loaded");
            return;
        }
        recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
            sitekey: SITE_KEY,
            size: 'normal',
            callback: onRecaptchaSuccess,
            'expired-callback': onRecaptchaExpired
        });
    }

    function resetRecaptcha(){
        if(recaptchaWidgetId.value !== null){
            window.grecaptcha.reset(recaptchaWidgetId.value);
            recaptchaToken.value = '';
        }
    }

    onMounted(() => {
        const interval = setInterval(() => {
            if(window.grecaptcha && window.grecaptcha.render){
                renderRecaptcha();
                clearInterval(interval)
            }
        }, 100);

        onBeforeUnmount(() => { 
            clearInterval(interval)
        })
    });
</script>