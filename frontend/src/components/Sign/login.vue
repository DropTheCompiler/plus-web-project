<template>
	<div class="login">
		<br /><br />
		<div class="tag">I D )</div>
		<input type="text" id="id" v-model="user.userid" placeholder="ID를 입력해주세요!" /><br /><br />
		<div class="tag">P W )</div>
		<input
			v-on:keydown.enter="login"
			type="password"
			id="password"
			v-model="user.password"
			placeholder="PW를 입력해주세요!"
		/>
		<br /><br />
		<button v-on:click="login">로그인</button>
		<p>
			만약, 계정이 없다면,
			<a @click="signUp">
				회원가입을 먼저 진행해주세요!
			</a>
		</p>
	</div>
</template>

<script>
export default {
	data: function () {
		return {
			redirect: this.$route.query.redirect,
			user: {
				userid: '',
				password: '',
			},
		};
	},
	methods: {
		login: function () {
			this.$http
				.post('/api/users/login', {
					user: this.user,
				})
				.then(res => {
					if (res.data.success === true) {
						// Session setting / 세션 지정
						this.$session.set('user_idx', res.data.user_idx);
						this.$session.set('userid', res.data.userid);
						this.$session.set('name', res.data.name);

						this.UpdateRating();

						// 등급 표시
						this.$http.get(`/api/users/check/${this.$session.get('user_idx')}`).then(res => {
							this.$session.set('rating', res.data.rating);
						});
						alert(res.data.message);
						this.$router.push(this.redirect);
					}
					if (res.data.success === false) {
						// Session removing / 세션 삭제
						this.$session.remove('user_idx');
						this.$session.remove('userid');
						this.$session.remove('name');
						this.$session.remove('rating');
						alert(res.data.message);
					}
				})
				.catch(() => {
					alert('알 수 없는 오류가 발생했습니다');
				});
		},
		signUp() {
			this.$router.push({
				path: '/SignUp',
				query: { redirect: this.$route.fullPath },
			});
		},
		UpdateRating() {
			this.$http.get(`/api/users/update/${this.$session.get('user_idx')}`).then();
		},
	},
};
</script>

<style scoped>
.login {
	margin-top: 40px;
	text-align: center;
}

.tag {
	position: relative;
	margin-left: -210px;
	margin-bottom: 5px;
	font-size: 15px;
	color: black;
}

input {
	margin: 0px 0;
	width: 22%;
	padding: 15px;
}

button {
	margin-top: 10px;
	width: 10%;
	cursor: pointer;
	background-color: white;
	border: 0px;
	height: 0px;
}

p {
	margin-top: 40px;
	font-size: 15px;
	color: #248657;
}

p a {
	text-decoration: underline;
	cursor: pointer;
	color: #df0174;
}
</style>
