<script>
  import firebaseConfig from './firebaseConfig';
  import firebase from '@firebase/app';
  import '@firebase/auth';

  if (firebase.apps.length === 0) {
    firebase.initializeApp(firebaseConfig);
  }

  export let user;

  async function login() {
    const result = await firebase
      .auth()
      .signInWithPopup(new firebase.auth.GoogleAuthProvider());
    user = result.user;
  }

  async function logout() {
    await firebase.auth().signOut();
    user = null;
  }
</script>

<style>
  div.login {
    display: block;
  }
  img.photo {
    height: 2em;
  }
</style>

<div class:logged-in={!!user} class="login">
  {#if user}
    <img class="photo" src={user.photoURL} alt={user.displayName} />
    <div>
      <p>{user.displayName}</p>
      <button on:click={logout}>Sign Out</button>
    </div>
  {:else}
    <button on:click={login}>Sign In</button>
  {/if}
</div>
