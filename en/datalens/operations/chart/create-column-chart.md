# Creating a column chart

To create a column chart:

1. On the {{ datalens-full-name }} homepage, click **Create chart**.
1. Under **Dataset**, select a dataset to visualize. If you don't have a dataset, [create one](../dataset/create.md).
1. Select **Column chart** as the chart type.
1. Drag a dimension from the dataset to the **X** section. The values are displayed in the lower part of the chart on the X-axis.
1. Drag one or more indicators from the dataset to the **Y** section. The values are displayed as columns on the Y-axis.

   {% include [chart-signature-note](../../../_includes/datalens/operations/datalens-chart-signature-note.md) %}

A stacked column chart is displayed by default.

Learn more about the chart sections in [{#T}](../../concepts/chart/types.md#bar-chart) and [{#T}](../../concepts/chart/types.md#normalized-bar-chart).

## Creating a grouped column chart {#grouped-column-chart}

To display an X-axis grouped column chart:

1. Go to the column chart that you created.

1. Depending on the number of measures in the **Y** section, follow these steps:

   {% list tabs %}

   - One measure

     1. Check if there is a dimension in the **Colors** section.
     1. Duplicate this dimension in the **X** section. The sequence of dimensions affects the grouping order.

     <iframe src="https://datalens.yandex/3qmmquiu7i1ux?_embedded=1&_theme=system" width="600" height="400" frameborder="0"></iframe>

   - Two or more measures
   
     1. Drag the `Measure Names` dimension to the **Colors** section.
     1. Drag the `Measure Names` dimension to the **X** section. The sequence of dimensions affects the grouping order.

     <iframe src="https://datalens.yandex/tgc7ep00pz26n?_embedded=1&_theme=system" width="600" height="400" frameborder="0"></iframe>

   {% endlist %}

## Configuring the display of `null` values {#null-settings}

{% include [datalens-chart-null-settings](../../../_includes/datalens/datalens-chart-null-settings.md) %}

## Column color based on a measure {#column-colors}

To color columns in a chart based on the value of a measure:

1. Go to the column chart that you created.
1. Depending on the number of measures in the **Y** section, follow the steps below:

   {% list tabs %}

   - One measure

      Copy the measure from the **Y** section to the **Colors** section.

      Columns in the chart will take on colors as a function of the measure values.

      ![image](../../../_assets/datalens/operations/chart/column-colors-1.png)

   - Two or more measures

      Drag the `Measure Values` measure to the **Colors** section.

      The columns in the chart will take on colors depending on the values of all the measures listed in the **Y** section.

      ![image](../../../_assets/datalens/operations/chart/column-colors-2.png)

   {% endlist %}

1. Configure a color gradient for the measure as well. To do this, in the top right-hand corner of the **Colors** section, click ![image](../../../_assets/datalens/gear.svg) (the icon is displayed when you mouse over the section).
1. In the color settings, specify:

   * **Gradient type**: Select 2 or 3 colors.
   * Gradient color: Select a color palette for the gradient from the list.
   * Gradient direction: Change the gradient direction using the ![image](../../../_assets/datalens/swap.svg) icon.
   * **Set threshold values**: Set numeric thresholds for each color. Works if the **Y** section contains a single value.
