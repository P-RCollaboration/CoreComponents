<template>
    <div>
        <slot :validatehost="this"></slot>
    </div>
</template>

<script>
export default {
    name: `ValidateHost`,
    data() {
        return {
            registerElements: new WeakMap(),
            counter: 0,
            isValid: false
        }
    },
    methods: {
        clear(element) {
            if (this.registerElements.delete(element)) this.counter--;

            this.checkIsValid();
        },
        add(element, rule) {
            this.registerElements.set(element, rule);
            this.counter++;

            this.checkIsValid();
        },
        checkIsValid() {
            this.isValid = this.counter === 0;
            this.$emit(`validatechanged`, this.isValid);
        }
    }
};
</script>