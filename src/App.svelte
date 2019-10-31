<script>
	import Word from './Word.svelte';
	import Alert from './Alert.svelte';
	import Info from './Info.svelte';
	import Confirm from './Confirm.svelte';
	let trie;
	let all_good = false;
	let text_input = "";
	let confirmed = false;
	let not_found = [];
	async function wasmImport () {
	  const { Trie } = await import('spell-checker-blueheart');
		return Trie.new();
	}

	async function loadDict(file) {
  	const dict = await  fetch(file)
    .then(t => t.text()).then(t => trie.load(t));
	}

	wasmImport().then(res => {
		trie = res;
		loadDict("checker.txt");
	});

	function search() {
		let words = text_input.split(/[^\w']+/);
		words = new Set(words);
    not_found = [...words].filter(w => !trie.search(w.toLowerCase()) && isNaN(w));
		if (!not_found.length) all_good = true;
		else all_good = false;
		confirmed = false;
	}

	function apply_changes() {
		not_found.map( (word, index) => {
			let edit = document.getElementById(`${index}`).innerText;
			if (word !== edit) {
				console.log(`${edit} <- ${word}`);
				text_input = text_input.replace(new RegExp(word, "g"), edit);
			}
			else {
				trie.new_word(word.toLowerCase());
			}
		});
		not_found = []
		confirmed = true;
	}
</script>

<div class="app min-h-screen min-v-screen p-8 bg-gray-slighest my-10 font-mono">
	<div class="row sm:flex">
		<div class="col sm:w-1/2">
			<div class="box rounded flex flex-col shadow border border-blue-600">
	      <div class="box__title bg-transparent border-b border-blue-600 text-blue-600 font-semibold py-2 px-4">Input</div>
				<textarea id="input_area" bind:value={text_input} class="form-textarea border-none mt-1 block w-full font-sans" rows="10" placeholder="Enter text"></textarea>
	    </div>
			<button on:click={search} class="bg-transparent hover:bg-red-600 text-red-600 font-semibold hover:text-white my-2 py-2 px-4 border border-red-600 hover:border-transparent rounded">
  			Verify
			</button>
		</div>
	  <div class="col mt-8 sm:ml-8 sm:mt-0 sm:w-1/2">
			<div class="box rounded">
			 <div class="row sm:flex mb-3">
				<div class="col sm:w-1/2 box__title bg-transparent border border-red-600 rounded mb-2 text-red-600 font-semibold py-2 px-4">Results</div>
				{ #if not_found.length }
					<Info />
				{ /if }
			</div>
			{ #if all_good }
				<Alert/>
			{ /if }
				{ #each not_found as word }
					<Word id={not_found.indexOf(word)} word={word}/>
				{ /each }
			</div>
				{ #if not_found.length }
					<button on:click={apply_changes} class="bg-transparent hover:bg-green-600 text-green-600 font-semibold hover:text-white my-5 py-2 px-4 border border-green-600 hover:border-transparent rounded">
  					Apply changes
					</button>
				{ /if }
	  </div>
		{ #if confirmed }
		<Confirm />
		{ /if }
	</div>
</div>
