<script>
    export let name;
    import { onMount } from "svelte";
    import { Button, Col, Row, Toast } from "sveltestrap";
    let count = 20000;
    let diff = 0;
    let cps = 0;
    let toastMessage = "";
    let isOpen = false;
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

    let unlockables = [
        {
            name: "cps1",
            itemBuff: "clicker",
            value: 0.05,
            pretty: "clicker +5%",
            price: 30000,
            purchased: false,
        },
    ];

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
            toastMessage = "Autosave complete";
            isOpen = true;
        }, 30000);
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
        diff = diff - items[item].cost;
        items[item].cost = Math.round(
            items[item].cost + Math.pow(items[item].rate, items[item].count)
        );
    };
    // console.log(Object.keys(items));

    const formatNumber = (number) => {
        return number.toLocaleString();
        // return number.toString().replace(/(.)(?=(\d{3})+$)/g, "$1,");
    };

    const clickUnlockable = (unlockable) => {
        for (let upgrade in unlockables) {
            if (unlockables[upgrade].name == unlockable) {
                items[unlockables[upgrade].itemBuff].value =
                    items[unlockables[upgrade].itemBuff].value +
                    unlockables[upgrade].value;
                unlockables[upgrade].purchased = true;
                toastMessage = "Upgrade purchased";
                isOpen = true;
            }
        }
    };
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
    <Toast autohide body {isOpen} on:close={() => (isOpen = false)}>
        {toastMessage}
    </Toast>
    <Row>
        <Col>
            <h2>Total: {formatNumber(count)}</h2>
        </Col>
        <Col>
            <h2>
                Coins per Second: {formatNumber(cps)}
            </h2>
        </Col>
    </Row>
    <Row>
        <Col md={6}>
            <Row>
                <Col>
                    <Button color="primary" on:click={() => count++}>
                        Get coin
                    </Button>
                </Col>
            </Row>
            <Row class="my-4">
                <Col>
                    <h4>Upgrades</h4>
                </Col>
            </Row>
            <Row>
                {#each unlockables as purchase}
                    <Col hidden={purchase.purchased ? "hidden" : undefined}>
                        <Button
                            on:click={() => clickUnlockable(purchase.name)}
                            color="success">{purchase.pretty}</Button
                        >
                    </Col>
                {/each}
            </Row>
        </Col>
        <Col md={6}>
            {#each Object.entries(items) as item}
                <Row>
                    <Col sm={8}>
                        {item[1].name} = +{item[1].value.toFixed(2)} cps
                        <Row>
                            <Col>
                                <small class="text-muted">
                                    ({item[1].count} total = {(
                                        item[1].count * item[1].value
                                    ).toFixed(2)} cps)
                                </small>
                            </Col>
                        </Row>
                    </Col>
                    <Col sm={4}>
                        <Button
                            disabled={item[1].cost > count
                                ? "disabled"
                                : undefined}
                            on:click={() => clickedItem(item[1].name)}
                            color="primary">{formatNumber(item[1].cost)}</Button
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
