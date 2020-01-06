<script>
	import LineItem from "./LineItem.svelte";
	import produce from "immer";
	import print from "print-js";
	import { get, writable } from "svelte/store";
	import Contact from "./Contact.svelte";
	import BusinessInfo from "./BusinessInfo.svelte";
	let daysWorked = 10;
	let dayrate = 100;
	let gross, vat, net;
	let title = "November 2019";
	let reference = "234234";

	const format = number => Number(number).toLocaleString();

	$: gross =
		lineItems.reduce((acc, curr) => {
			return acc + curr.days;
		}, 0) * dayrate;
	$: vat = gross * 0.2;
	$: net = gross + vat;
	let lineItems;
	$: lineItems = [{ description: "testing", days: 10 }];
</script>

<style>
	:global(input) {
		font-size: 30px;
		color: grey;
		padding: 10px;
	}

	.delete, button {
		cursor: pointer;
	}
	button {
		color: grey;
		padding: 10px;
		font-size: 15px;
	}

	.debug {
		font-size: 10px;
		color: grey;
	}

	fieldset {
		border: none;
		margin: 5px;
		padding: 5px;
	}

	#invoice {
		font-family: "Gelasio", serif;
		width: 800px;
		margin: 0 auto;
		font-size: 1.3em;
	}

	.totals {
		text-align: right;
		bottom: 0;
	}

	.itemised {
		display: flex;
		flex-direction: column;
		justify-content: space-evenly;
		border: 1px solid rgb(0, 0, 0, 0.1);
		min-height: 400px;
	}
</style>

<div id="configure">

	<!-- <span on:click={() => print('invoice', 'html')}>print</span> -->

	<fieldset>
		<label for="title">title</label>
		<input type="text" bind:value={title} />
	</fieldset>
	<fieldset>
		<label for="reference">reference</label>
		<input type="text" bind:value={reference} />
	</fieldset>

	<fieldset>
		<label for="dayrate">day rate</label>
		<input type="number" id="dayrate" bind:value={dayrate} />
	</fieldset>

	<hr />

	<ul>
		{#each lineItems as item, index}
			<li>
				<LineItem bind:description={item.description} bind:days={item.days} />
				<span class="delete" on:click={()=>{
					lineItems = lineItems.filter((item, i)=>i!==index)
				}}>delete</span>
			</li>
		{/each}
	</ul>

	<button
		on:click={() => {
			lineItems = lineItems.concat({ description: '', days: 0 });
		}}>
		add line item
	</button>
	<hr />

	<span class="debug">{JSON.stringify(lineItems)}</span>

	<hr />
</div>

<div id="invoice">
	<Contact />
	<h1>{title}</h1>
	<strong>ref: {reference}</strong>

	<ul class="itemised">
		{#each lineItems as item, index}
			<li>
				{item.description}: {item.days} days £{format(item.days * dayrate)}
			</li>
		{/each}
	</ul>

	<div class="totals">
		<fieldset>
			<label for="gross">gross</label>
			£{format(gross)}
		</fieldset>
		<fieldset>
			<label for="net">net</label>
			£{format(net)}
		</fieldset>
	</div>
	<BusinessInfo />
</div>
