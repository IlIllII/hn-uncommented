<script>
export default {
    data() {
        return {
            checked: false
        }
    },
    methods: {
        toggleWeekly() {
            this.$emit('toggle-weekly', this.checked)
        }
    },
}
</script>


<template>
    <div class="edition-slider">
        <input type="checkbox" id="editionToggle" class="edition-toggle" v-model="checked" @change="this.toggleWeekly">
        <label for="editionToggle" class="edition-label">
            <span class="edition-text">Weekly</span>
            <span class="edition-text">Daily</span>
            <span class="edition-handle"></span>
        </label>
    </div>
</template>


<style scoped>
span {
    margin-left: 1rem;
    color: #828282;
    font-size: 0.8rem;
}

.edition-slider {
    margin: auto;
    padding: 0 0 1rem 0;
    position: relative;
    display: inline-block;
    width: 200px;
    user-select: none;
}

.edition-toggle {
    opacity: 0;
    width: 0;
    height: 0;
}

.edition-label {
    display: flex;
    align-items: center;
    justify-content: space-around;
    cursor: pointer;
    background-color: var(--color-background);
    border-radius: 12px;
    border: solid 1px #ccc;
    padding: 5px;
    position: relative;
}

.edition-text {
    z-index: 2;
}

.edition-handle {
    position: absolute;
    top: 4px;
    left: -11px;
    width: 107px;
    height: 22px;
    background-color: white;
    background-color: var(--color-background);
    border: solid 1px #ccc;
    border-radius: 8px;
    transition: 0.4s;
    z-index: 1;
}

.edition-toggle:checked+.edition-label .edition-handle {
    transform: translateX(95px) scaleX(0.75);
}
</style>