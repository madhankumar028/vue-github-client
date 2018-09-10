<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <form v-on:submit.prevent="queryGitHub(query)" novalidate>
      	<input type="text" placeholder="GitHub username" v-model="query" />
    </form>
    <div class="results">
		<div class="result-container" v-if="results">
			<div class="avatar-container">
				<img class="avatar" v-bind:src="results.avatar_url" />
			</div>
			<div class="profile-info">
				<h2>{{ results.name }}</h2>
				<div class="bio">{{ results.bio }}</div>
				<div class="followers">
					<div class="pill red-pill" v-if="results.followers">Followers: {{ results.followers }}</div>
					<div class="pill blue-pill" v-if="results.following">Following: {{ results.following }}</div>
					<a class="pill lite-pill" v-if="results.blog" v-bind:href="results.blog">View web</a>
				</div>	
			</div>
		</div>
		<div class="alert alert-danger" v-if="userError">{{userError}}</div>
		<div class="panel panel-primary" v-if="repos.length">
			<div class="panel-body">
				<ul>
					<li class="list-group-item" v-for="repo in repos">{{repo.name}}</li>
				</ul>
			</div>
		</div>
		<div class="alert alert-danger" v-if="repoError">{{repoError}}</div>		
    </div>
  </div>
</template>

<script>
export default {
	name: 'GitHub',
	data () {
		return {
			msg: 'GitHub Search',
			query: '',
			results: null,
			repos: [],
			userError: '',
			repoError: '',
		}
	},
	methods: {
		queryGitHub(username) {
			let self = this;
			self.results = null;
			self.userError = '';
			fetch(`https://api.github.com/users/${username}`)
			.then((j) => {
				if (j.ok) {
					return j.json();
				} else if (j.status === 403) {
					self.userError = 'Request Forbidden to find the user';
				} else {
					self.userError = 'User Not Found';
					throw new Error('Something went wrong');
				}
			}).then((r) => {
				console.log(r);
				self.results = r;
			});
			this.getUserRepos(username);
		},

		getUserRepos(username) {
			let self = this;
			self.repos.length = 0;
			self.repoError = '';
			fetch(`https://api.github.com/users/${username}/repos`)
			.then((j) => {
				if (j.ok) {
					return j.json();
				} else if (j.status === 403) {
					self.repoError = 'Request Forbidden to get all the repos';
				} else {
					self.repoError = 'No repos Found';
					throw new Error('Something went wrong');
				}
			}).then((r) => {
				self.repos = r;
			});
		}
	},
};
</script>

<style>
h1, h2 {
    font-weight: normal;
}
a {
	text-decoration: none;
}
.results {
    margin: 50px auto;
	max-width: 50%;
}
.result-container {
	height: 100px;
}
.avatar-container {
	display: inline-block;
	float: left;
	margin-right: 20px;
}
.avatar {
    width: 150px;
	border-radius: 50%;
}
.profile-info {
	text-align: left;
}
.bio {
    white-space: nowrap;
}
.followers {
	margin-top: 10px;
}
ul {
    list-style-type: none;
    padding: 0;
}
li {
    display: inline-block;
    margin: 0 10px;
}
.alert {
	border: 1px solid #fff;
	font-size: 75%;
    font-weight: 700;
    line-height: 1;
    color: #fff;
    text-align: center;
    white-space: nowrap;
    vertical-align: baseline;
    border-radius: 1.25em;
	padding: .2em .6em .3em;
}
.alert-danger {
	background-color: red;
}
.pill {
	display: inline-block;
	border: 1px solid #fff;
	font-size: 75%;
    font-weight: 700;
    line-height: 1;
    color: #fff;
    text-align: center;
    white-space: nowrap;
    vertical-align: baseline;
    border-radius: 1.25em;
	padding: .2em .6em .3em;
}
.red-pill {
	background-color: #d9534f;
}
.blue-pill {
	background-color: #5bc0de;
}
.lite-pill {
	background-color: #337ab7;
}
.panel {
	margin-top: 60px;
	margin-bottom: 20px;
    background-color: #fff;
    border: 1px solid transparent;
    border-radius: 4px;
    -webkit-box-shadow: 0 1px 1px rgba(0,0,0,.05);
    box-shadow: 0 1px 1px rgba(0,0,0,.05);
}
.panel-primary {
	background-color: #337ab7;
}
.panel-body {
	padding: 15px;
}
.list-group-item {
	position: relative;
    display: block;
    padding: 10px 15px;
    margin-bottom: -1px;
    background-color: #fff;
    border: 1px solid #ddd;
}
</style>
