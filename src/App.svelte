<script>
    export let name;
    import { onMount } from "svelte";
    import { Button, Col, Row } from "sveltestrap";
    let count = 20000;
    let diff = 0;
    let cps = 0;

    let items = {
        clicker: {
            name: "clicker",
            cost: 10,
            count: 0,
            value: 0.2,
            rate: 1.15,
        },
        gpu: {
            name: "gpu",
            cost: 100,
            count: 0,
            value: 1,
            rate: 1.2,
        },
        factory: {
            name: "factory",
            cost: 1000,
            count: 0,
            value: 5,
            rate: 1.25,
        },
        blockchain: {
            name: "blockchain",
            cost: 20000,
            count: 0,
            value: 30,
            rate: 1.3,
        },
    };

    onMount(() => {
        let storageItems = JSON.parse(localStorage.getItem("items"));
        if (
            storageItems != undefined &&
            localStorage.getItem("count") != undefined
        ) {
            items = storageItems;
            count = parseInt(localStorage.getItem("count"));
        }
        // CPS
        setInterval(() => {
            cps = (count - diff).toFixed(2);
            diff = count;
        }, 1000);

        // set count
        setInterval(() => {
            count = getCps();
            // console.log(cost);
        }, 1000);

        // save
        setInterval(() => {
            localStorage.setItem("items", JSON.stringify(items));
            localStorage.setItem("count", count);
        }, 10000);
    });

    const getCps = () => {
        let allCount = count;
        for (const [key, value] of Object.entries(items)) {
            allCount = allCount + value.count * value.value;
        }
        return allCount;
    };

    const clickedItem = (item) => {
        items[item].count++;
        count = count - items[item].cost;
        items[item].cost = Math.round(
            items[item].cost + Math.pow(items[item].rate, items[item].count)
        );
        cps = getCps();
        diff = count;
    };
    // console.log(Object.keys(items));
</script>

<svelte:head>
    <link
        rel="stylesheet"
        href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.0/dist/css/bootstrap.min.css"
    />

    <link
        rel="stylesheet"
        href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.5.0/font/bootstrap-icons.css"
    />
</svelte:head>

<main>
    <Row>
        <Col>
            <h2>Total: {count.toFixed(2)}</h2>
        </Col>
        <Col>
            <h2>
                Coins per Second: {cps}
            </h2>
        </Col>
    </Row>
    <Row>
        <Col md={6}>
            <Button color="primary" on:click={() => count++}>Get coin</Button>
        </Col>
        <Col md={6}>
            {#each Object.entries(items) as item}
                <Row>
                    <Col sm={8}
                        >{item[1].name} = +{item[1].value} cps ({item[1].count} total
                        = {(item[1].count * item[1].value).toFixed(2)} cps)</Col
                    >
                    <Col sm={4}>
                        <Button
                            disabled={item[1].cost > count
                                ? "disabled"
                                : undefined}
                            on:click={() => clickedItem(item[1].name)}
                            color="primary">{item[1].cost}</Button
                        >
                    </Col>
                </Row>
            {/each}
        </Col>
    </Row>
</main>

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
