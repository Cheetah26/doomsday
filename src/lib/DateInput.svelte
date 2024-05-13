<script context="module">
	let DateInputCounter = 0;
</script>

<script>
	/** @type {Date} */
	export let date;

	const id = `dateinput_${DateInputCounter++}`;
</script>

<label for={id}><slot /> </label>
<input
	{id}
	type="date"
	value={date.toISOString().split('T')[0]}
	on:change={({ target: { value: v } }) => (date = v ? new Date(v) : new Date())}
/>
