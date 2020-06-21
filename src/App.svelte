<script>
  import Login from './firebase/Login.svelte';
  import Guestbook from './firebase/Guestbook.svelte';
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>Welcome to my Insta!!</h1>
  <Login />
  <Guestbook />
</main>
