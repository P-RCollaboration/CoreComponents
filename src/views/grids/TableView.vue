<template @core>
    <div class="table-view-container">
        <div class="table-grid-container" :style="{ 'grid-template-columns': getColumnSizes() }">
            <div
                v-for="(column, index) in context.visibleColumns"
                :key="column.field"
                :style="headerCell.getHeaderCellStyle(column, index, headerCell)">
                <slot
                name="columnhead"
                :column="column">
                    {{ column.title }}
                </slot>
            </div>
            <div
                v-for="(item, index) in gridItems()"
                :key="index"
                :style="cell.getCellStyle(item, cell)">
                <slot
                v-if="item.column.slot"
                :name="item.column.slot"
                :item="item">
                    {{ item.value }}
                </slot>
                <span v-else>
                    {{ item.value }}
                </span>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: `TableView`,
    props: {
        context: {
            type: Object,
            required: true
        },
        themeHeaderCell: {
            type: Object,
            default:  () => {
            return {};
            }
        },
        themeCell: {
            type: Object,
            default:  () => {
            return {};
            }
        },
        fillItemsHandler: {
            type: Function
        }
    },
    data() {
        return {
            headerCell: {
                leftCellStyle: {
                    'border-width': `1px 0 1px 1px`,
                    'border-style': `solid`,
                    'border-bottom-color': `rgb(186, 191, 199)`,
                    'border-left-color': `rgb(186, 191, 199)`,
                    'border-top-color': `rgb(186, 191, 199)`,
                    'border-radius': `3px 0 0 0`
                },
                middleCellStyle: {
                    'border-width': `1px 0 1px 0`,
                    'border-style': `solid`,
                    'border-bottom-color': `rgb(186, 191, 199)`,
                    'border-top-color': `rgb(186, 191, 199)`
                },
                rightCellStyle: {
                    'border-width': `1px 1px 1px 0`,
                    'border-style': `solid`,
                    'border-bottom-color': `rgb(186, 191, 199)`,
                    'border-right-color': `rgb(186, 191, 199)`,
                    'border-top-color': `rgb(186, 191, 199)`,
                    'border-radius': `0 3px 0 0`
                },
                getHeaderCellStyle: this.getHeaderCellStyle
            },
            cell: {
                leftCellStyle: {
                    'border-width': `0 0 1px 1px`,
                    'border-style': `solid`,
                    'border-bottom-color': `rgb(186, 191, 199)`,
                    'border-left-color': `rgb(186, 191, 199)`,
                    'border-top-color': `rgb(186, 191, 199)`
                },
                middleCellStyle: {
                    'border-width': `0 0 1px 0`,
                    'border-style': `solid`,
                    'border-bottom-color': `rgb(186, 191, 199)`,
                    'border-top-color': `rgb(186, 191, 199)`
                },
                rightCellStyle: {
                    'border-width': `0px 1px 1px 0`,
                    'border-style': `solid`,
                    'border-bottom-color': `rgb(186, 191, 199)`,
                    'border-right-color': `rgb(186, 191, 199)`,
                    'border-top-color': `rgb(186, 191, 199)`
                },
                getCellStyle: this.getCellStyle,
                fillCellStyle: this.fillCellStyle
            }
        }
    },
    created() {
        if (this.themeHeaderCell) {
            for (const key of Object.keys(this.themeHeaderCell)) {
            if (!(key in this.headerCell)) continue;
            
            this.headerCell[key] = this.themeHeaderCell[key];
            }
        }

        if (this.themeCell) {
            for (const key of Object.keys(this.themeCell)) {
            if (!(key in this.cell)) continue;

            this.cell[key] = this.themeCell[key];
            }
        }
    },
    methods: {
        getColumnSizes() {
            return this.context.visibleColumns.map(a => a.columnPoints && a.actualWidth ? `${a.actualWidth}${a.columnPoints}` : `1fr`).join(` `);
        },
        getHeaderCellStyle(column, index, theme) {
            const styles = {
                'background-color': `rgb(252, 252, 252)`,
            };

            const lastColumns = this.context.visibleColumns.length - 1;

            return Object.assign(
                styles,
                index === 0 ? theme.leftCellStyle : (index === lastColumns ? theme.rightCellStyle : theme.middleCellStyle) 
            );
        },
        fillCellStyle(cell, theme, styles) {
            const lastColumns = this.context.visibleColumns.length - 1;

            return Object.assign(
                styles,
                cell.columnIndex === 0 ? theme.leftCellStyle : (cell.columnIndex === lastColumns ? theme.rightCellStyle : theme.middleCellStyle) 
            );
        },
        getCellStyle(cell, theme) {
            const styles = {
                'background-color': cell.rowIndex % 2 === 0 ? `` : `rgb(252, 252, 252)`,
            };

            return this.cell.fillCellStyle(cell, theme, styles);
        },
        gridItems() {
            return this.context.handlers.fillItems(this.context.visibleColumns, this.context);
        }
    }
}
</script>

<style>
.table-view-container {
  width: 100%;
  display: flex;
  flex-direction: column;
}
.table-container {
  width: 100%;
  table-layout: fixed;
}
.table-container th {
  background-color: rgb(248, 248, 248);
  border-color:rgb(186, 191, 199);
  border-style: solid;
  border-width: 1px;
}
.table-grid-container {
  display: grid;
  column-gap: 0px;
  row-gap: 0px;
}
</style>