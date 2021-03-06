<script setup lang="ts">
import { useUiStore } from "../../stores/ui";
const authState = ref<"Login" | "Signup">("Login");

const { signUp, signIn, user, authError } = useAuth();

const showConfirmEmailMsg = ref(false);

const router = useRouter();

const input = reactive({
	email: "",
	password: "",
});

const rememberMe = ref(false);

const toggleAuthState = () => {
	if (authState.value === "Login") {
		authState.value = "Signup";
	} else {
		authState.value = "Login";
	}
	authError.value = "";
	showConfirmEmailMsg.value = false;
};

const clearInput = () => {
	input.email = "";
	input.password = "";
};
const uiStore = useUiStore();
const handleSubmit = async () => {
	if (authState.value === "Login") {
		await signIn({ email: input.email, password: input.password });
		clearInput();
		uiStore.toggleLoading(true);
		router.push("/");
	} else {
		await signUp({ email: input.email, password: input.password });
		clearInput();
		showConfirmEmailMsg.value = true;
	}
};
</script>

<template>
	<div class="max-width trans">
		<div class="card trans dark:text-darkSecondary dark:bg-darkBg bg-white">
			<div v-if="(!user && !showConfirmEmailMsg) || (!user && authError !== '')">
				<h3>{{ authState }}</h3>
				<div class="form-group">
					<label for="email">Email</label>
					<input
						class="w-sm mx-auto border shadow-sm border-slate-300 placeholder-slate-400 dark:bg-darkBg dark:focus:outline-darkPrimary"
						:type="'text'"
						:placeholder="'you@email.com'"
						v-model="input.email"
					/>
				</div>
				<div class="form-group">
					<label for="password">Password</label>
					<input
						v-model="input.password"
						type="password"
						placeholder="password"
						class="w-sm mx-auto border shadow-sm border-slate-300 placeholder-slate-400 dark:focus:outline-darkPrimary dark:bg-darkBg"
					/>
				</div>
				<div class="flex flex-col gap-4">
					<UiButton class="mt-4" @click="handleSubmit" mode="reverse">
						{{ authState }}
					</UiButton>
					<div class="flex items-center justify-center">
						<label>Remember me</label>
						<!-- <font-awesome-icon :icon="['fas', 'adjust']" /> -->
						<input class="checkbox" type="checkbox" v-model="rememberMe" />
					</div>
					<p id="invalid" v-if="authError !== ''">{{ authError }}</p>
					<p v-if="authState === 'Login'" class="flex justify-center">
						Need to create an Account?
						<span class="link px-2" @click="toggleAuthState"> Signup</span>
					</p>
					<p v-if="authState === 'Signup'" class="flex justify-center">
						Already have an account?
						<span class="link px-2" @click="toggleAuthState"> Login</span>
					</p>
				</div>
			</div>
			<div
				class="flex mx-auto flex-col"
				v-if="!user && showConfirmEmailMsg && authError === ''"
			>
				<p class="text-center items-center mx-auto">
					Please check your email to confirm your account
				</p>
				<span
					class="flex mx-auto py-5 hover:text-gray-300 hover:cursor-pointer"
					@click="showConfirmEmailMsg = false"
					>Dismiss</span
				>
			</div>
		</div>
	</div>
</template>

<style scoped>
.max-width {
	max-width: 600px;
	margin: 0 auto;
}
.card h3 {
	font-size: 3rem;
	padding: 0rem 0 1rem 0;
	text-align: center;
}
.form-group label {
	display: block;
	margin: 0.5rem 0;
	text-align: center;
}
.form-group input {
	width: 100%;
	padding: 0.5rem;
	border: 1px solid #ccc;
	border-radius: 3px;
}
.form-group button {
	width: 100%;
	padding: 0.5rem;
	border: 1px solid #ccc;
	border-radius: 5px;
	margin: 2rem 0 0 0;
}
.form-group p {
	text-align: center;
	margin: 1rem 0;
}
.link {
	text-decoration: underline;
	cursor: pointer;
}
.link:hover {
	color: var(--hover-color);
}
#invalid {
	color: rgb(255, 108, 108);
	text-align: center;
	margin: 1rem 0;
}
.btn:hover {
	background-color: var(--hover-color);
	color: white;
}
</style>
