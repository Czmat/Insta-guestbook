<script>
  import firebase from '@firebase/app';
  // needed for side-effect
  import '@firebase/firestore';
  import '@firebase/storage';

  let emptyRecord = { title: '', file: null };
  let record = { ...emptyRecord };
  let records = [];
  let errorMessage;

  const collection = firebase.firestore().collection('guestbook');
  const storageRef = firebase.storage().ref();

  collection.onSnapshot(
    snap => (records = snap.docs.map(d => [d.id, d.data()]))
  );

  async function submit(e) {
    await collection.add(record);
    record = { ...emptyRecord };
    e.target.reset();
  }

  async function remove(id, file) {
    if (file) {
      await storageRef.child(file).delete();
    }
    await collection.doc(id).delete();
  }

  async function upload(e) {
    errorMessage = '';
    if (e.target.files && e.target.files.length > 0) {
      const file = e.target.files[0];
      const findImg = records.filter(rec => rec[1].file === file.name);

      if (findImg.length === 0) {
        if (window.BrowserImageResizer) {
          const blob = await window.BrowserImageResizer.readAndCompressImage(
            file,
            {
              quality: 0.8,
              maxWidth: 400,
              maxHeight: 400,
            }
          );
          await storageRef.child(file.name).put(blob);
        } else {
          await storageRef.child(file.name).put(file);
        }
      } else {
        errorMessage = 'This picture already uploaded, find another picture';
      }
      record.file = file.name;
    }
  }
</script>

<style>
  .record {
    position: relative;
  }

  .record .delete {
    position: absolute;
    top: 0px;
    right: 0px;
  }
</style>

<svelte:head>
  <script
    src="https://cdn.jsdelivr.net/gh/ericnograles/browser-image-resizer@2.0.1/dist/index.js">

  </script>
</svelte:head>

<form on:submit|preventDefault={submit}>
  <input bind:value={record.title} placeholder="say something" required />
  <br />
  <input type="file" accept="image/*" on:change={upload} />
  <br />
  {#if errorMessage}
    <p>{errorMessage}</p>
  {/if}
  <button
    type="submit"
    disabled={!record.title || !record.file || errorMessage}>
    Submit
  </button>
</form>

<ul>
  {#each records as [id, rec] (id)}
    <li title={id} class="record">
      <button class="delete" on:click={() => remove(id, rec.file)}>
        delete
      </button>
      <p>{rec.title}</p>
      {#if rec.file}
        {#await storageRef.child(rec.file).getDownloadURL()}
          <p>loading ...</p>
        {:then url}
          <img src={url} alt={rec.file} />
        {/await}
      {/if}
    </li>
  {/each}
</ul>
