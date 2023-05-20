<script>
	import "carbon-components-svelte/css/g90.css";
	import { TextInput } from "carbon-components-svelte";
	import { Button } from "carbon-components-svelte";

	let riskValue;
	let nValue;
	let riskRewardRatio;
	let symbolName;
	export let resultObject;
    export let isCalculated = false;
	const apiKey = "97YJ2A1080QGQR7X";
	const apiKey2 = "ch8m9j1r01qtgm5dnrh0ch8m9j1r01qtgm5dnrhg";
	const delay = (ms) => new Promise((res) => setTimeout(res, ms));
	let atr;
	let sharePriceAA;
	async function fetchData() {
		try {
			let url = `https://www.alphavantage.co/query?function=ATR&symbol=${symbolName}&interval=daily&time_period=14&apikey=${apiKey}`;
			let url2 = `https://finnhub.io/api/v1/quote?symbol=${symbolName}&token=${apiKey2}`;
			const response = await fetch(url);
			if (response.ok) {
				const data = await response.json();
				const today = new Date();
				const dayOfWeek = today.getDay();
				const daysToSubtract =
					dayOfWeek === 0 ? 2 : dayOfWeek === 1 ? 3 : 1;
				const yesterday = new Date();
				yesterday.setDate(today.getDate() - daysToSubtract);
				const year = yesterday.getFullYear();
				const month = ("0" + (yesterday.getMonth() + 1)).slice(-2);
				const day = ("0" + yesterday.getDate()).slice(-2);
				const yesterdayDate = `${year}-${month}-${day}`;

				atr = data["Technical Analysis: ATR"][yesterdayDate]["ATR"];

				const response2 = await fetch(url2);
				if (response2.ok) {
					const data2 = await response2.json();
					sharePriceAA = data2.c;
				} else {
					console.error("Error:", response2.status);
				}
			} else {
				console.error("Error:", response.status);
			}
		} catch (error) {
			console.error("Error:", error.message);
		}
	}

	async function SubmitValues() {
		await fetchData();
		resultObject = calculateTrade(
			sharePriceAA,
			atr,
			riskValue,
			riskRewardRatio,
			nValue
		);
        isCalculated = true
	}
	function calculateTrade(price, atr, risk, rr, n) {
		const stopLoss = price - n * atr;
		const positionSize = risk / (n * atr);
		const takeProfit = price + n * atr * rr;

		return {
			stopLoss: stopLoss,
			takeProfit: takeProfit,
			shares: positionSize,
		};
	}
</script>

<div class="custom-input">
	<h1><a href="https://www.investopedia.com/articles/trading/08/turtle-trading.asp" target="_blank">What is this?</a></h1>
	<br>
	<h3>Risk:</h3>
	<TextInput type="number" bind:value={riskValue} />
	<h3>N:</h3>
	<TextInput type="number" bind:value={nValue} />
	<h3>Risk-Reward Ratio:</h3>
	<h5>This should be the value of 1:(Your number should go here)</h5>
	<TextInput type="number" bind:value={riskRewardRatio} />
	<h3>Symbol:</h3>
	<TextInput type="text" bind:value={symbolName} />
</div>

<br />
<br />
<Button on:click={SubmitValues}>Calculate!</Button>

<style>
	.custom-input {
		width: 500px; /* Adjust the desired width here */
	}
</style>
