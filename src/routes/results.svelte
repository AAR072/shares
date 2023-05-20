<script>
    import { Accordion, AccordionItem } from "carbon-components-svelte";
    export let resultObject;
  
    let transformedResultObject;
  
    $: {
      if (resultObject) {
        transformedResultObject = {
          'Stop Loss': {
            value: resultObject.stopLoss,
            explanation: "Set your stop loss to this value"
          },
          'Take Profit': {
            value: resultObject.takeProfit,
            explanation: "Set your take profit to this value"
          },
          'Share Count': {
            value: resultObject.shares,
            explanation: "Set an order for this amount of shares"
          }
        };
      }
    }
  </script>
  
  <hr />
  
  {#if transformedResultObject}
    <Accordion>
      {#each Object.entries(transformedResultObject) as [key, { value, explanation }]}
        <AccordionItem title={key}>
          <p>{value}</p>
          <p>{explanation}</p>
        </AccordionItem>
      {/each}
    </Accordion>
  {/if}
  