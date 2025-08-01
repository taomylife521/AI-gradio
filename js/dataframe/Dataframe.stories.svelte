<script lang="ts">
	// @ts-nocheck
	import { Meta, Template, Story } from "@storybook/addon-svelte-csf";
	import Table from "./shared/Table.svelte";
	import { within } from "@testing-library/dom";
	import { userEvent } from "@storybook/test";
	import { get } from "svelte/store";
	import { format } from "svelte-i18n";
	import Image from "@gradio/image";
</script>

<Meta
	title="Components/DataFrame"
	component={Table}
	parameters={{
		test: {
			dangerouslyIgnoreUnhandledErrors: true // ignore fullscreen permission error
		}
	}}
	argTypes={{
		editable: {
			control: [true, false],
			description: "Whether the DataFrame is editable",
			name: "interactive",
			value: true
		},
		wrap: {
			control: "boolean",
			description: "Whether text should wrap or truncate with ellipsis",
			defaultValue: false
		},
		column_widths: {
			control: "object",
			description: "Width of each column (px, %, or auto)",
			defaultValue: ["200px", "200px", "200px"]
		}
	}}
/>

<Template let:args>
	<Table {...args} i18n={get(format)} />
</Template>

<Story
	name="Interactive dataframe"
	args={{
		values: [
			["Cat", 5],
			["Horse", 3],
			["Snake", 1]
		],
		headers: ["Animal", "Votes"],
		label: "Animals",
		col_count: [2, "dynamic"],
		row_count: [3, "dynamic"]
	}}
/>

<Story
	name="Interactive dataframe with label"
	args={{
		values: [
			["Cat", 5, true],
			["Horse", 3, false],
			["Snake", 1, false]
		],
		headers: ["Animal", "Votes", "Is Pet"],
		datatype: ["str", "number", "bool"],
		label: "Animals",
		show_label: true,
		col_count: [2, "dynamic"],
		row_count: [3, "dynamic"]
	}}
/>

<Story
	name="Interactive dataframe no label"
	args={{
		values: [
			["Cat", 5],
			["Horse", 3],
			["Snake", 1]
		],
		headers: ["Animal", "Votes"],
		metadata: null,
		label: "Animals",
		show_label: false,
		col_count: [2, "dynamic"],
		row_count: [3, "dynamic"]
	}}
/>

<Story
	name="Static dataframe"
	args={{
		values: [
			["Cat", 5, true],
			["Horse", 3, false],
			["Snake", 1, false]
		],
		headers: ["Animal", "Votes", "Is Pet"],
		datatype: ["str", "number", "bool"],
		label: "Animals",
		col_count: [3, "dynamic"],
		row_count: [3, "dynamic"],
		editable: false
	}}
	play={async ({ canvasElement }) => {
		// tests that the cell is not editable

		const canvas = within(canvasElement);
		const cells = canvas.getAllByRole("cell");
		const initial_value = cells[0].textContent;
		await userEvent.click(cells[0]);
		await userEvent.keyboard("new value");

		const final_value = cells[0].textContent;
		if (initial_value?.trim() !== final_value?.trim()) {
			throw new Error("Cell content changed when it should be non-editable");
		}

		const inputs = canvas.queryAllByRole("textbox");
		if (inputs.length > 0) {
			throw new Error("Input field appeared when table should be non-editable");
		}
	}}
/>

<Story
	name="Dataframe with different precisions"
	args={{
		values: [
			[1.24, 1.24, 1.24],
			[1.21, 1.21, 1.21]
		],
		headers: ["Precision=0", "Precision=1", "Precision=2"],
		display_value: [
			["1", "1.2", "1.24"],
			["1", "1.2", "1.21"]
		],
		label: "Numbers",
		col_count: [3, "dynamic"],
		row_count: [2, "dynamic"],
		editable: false
	}}
/>

<Story
	name="Dataframe with markdown and math"
	args={{
		values: [
			["Linear", "$y=x$", "Has a *maximum*  of 1 root"],
			["Quadratic", "$y=x^2$", "Has a *maximum*  of 2 roots"],
			["Cubic", "$y=x^3$", "Has a *maximum*  of 3 roots"]
		],
		headers: ["Type", "Example", "Roots"],
		datatype: ["str", "markdown", "markdown"],
		latex_delimiters: [{ left: "$", right: "$", display: false }],
		label: "Math",
		col_count: [3, "dynamic"],
		row_count: [3, "dynamic"],
		editable: false
	}}
/>

<Story
	name="Dataframe without a label"
	args={{
		values: [
			[800, 100, 800],
			[200, 800, 700]
		],
		headers: ["Math", "Reading", "Writing"],
		show_label: false,
		col_count: [3, "dynamic"],
		row_count: [2, "dynamic"],
		editable: false
	}}
/>

<Story
	name="Dataframe with different colors"
	args={{
		values: [
			[800, 100, 800],
			[200, 800, 700]
		],
		headers: ["Math", "Reading", "Writing"],

		styling: [
			[
				"background-color:teal; color: white",
				"1.2",
				"background-color:teal; color: white"
			],
			["1", "background-color:teal; color: white", "1.21"]
		],
		label: "Test scores",
		col_count: [3, "dynamic"],
		row_count: [2, "dynamic"],
		editable: false
	}}
/>

<Story
	name="Dataframe with column widths"
	args={{
		values: [
			[800, 100, 800],
			[200, 800, 700]
		],
		headers: ["Narrow", "Wide", "Half"],
		label: "Test scores",
		col_count: [3, "dynamic"],
		row_count: [2, "dynamic"],
		column_widths: ["20%", "30%", "50%"],
		editable: false
	}}
/>

<Story
	name="Dataframe with zero row count"
	args={{
		values: [],
		headers: ["Narrow", "Wide", "Half"],
		label: "Test scores",
		col_count: [0, "dynamic"],
		row_count: [0, "dynamic"],
		editable: false
	}}
/>

<Story
	name="Interactive dataframe with zero row count"
	args={{
		values: [],
		headers: ["Narrow", "Wide", "Half"],
		label: "Test scores",
		col_count: [0, "dynamic"],
		row_count: [0, "dynamic"],
		editable: true
	}}
/>

<Story
	name="Dataframe with link"
	args={{
		values: [['<a href="https://www.google.com/">google</a>']],
		headers: ["link"],
		datatype: ["markdown"],
		editable: false,
		col_count: [1, "dynamic"],
		row_count: [1, "dynamic"]
	}}
/>

<Story
	name="Dataframe with dialog interactions"
	args={{
		values: [
			[800, 100, 400],
			[200, 800, 700]
		],
		col_count: [3, "dynamic"],
		row_count: [2, "dynamic"],
		headers: ["Math", "Reading", "Writing"],
		editable: true
	}}
	play={async ({ canvasElement }) => {
		const canvas = within(canvasElement);

		const cell_400 = canvas.getAllByRole("cell")[5];
		await userEvent.click(cell_400);

		const open_dialog_btn =
			await within(cell_400).findByLabelText("Open cell menu");
		await userEvent.click(open_dialog_btn);

		const add_row_btn = canvas.getByText("Add row above");
		await userEvent.click(add_row_btn);

		const new_cell = canvas.getAllByRole("cell")[9];
		userEvent.click(new_cell);
	}}
/>

<Story
	name="Dataframe with fullscreen button and label and search"
	args={{
		col_count: [3, "dynamic"],
		row_count: [2, "dynamic"],
		headers: ["Math", "Reading", "Writing"],
		values: [
			[800, 100, 400],
			[200, 800, 700]
		],
		show_fullscreen_button: true,
		show_label: true,
		show_copy_button: true,
		show_search: "search",
		label: "Test scores"
	}}
/>

<Story
	name="Dataframe with multiple selection interactions"
	args={{
		values: [
			[1, 2, 3, 4],
			[5, 6, 7, 8],
			[9, 10, 11, 12],
			[13, 14, 15, 16]
		],
		col_count: [4, "dynamic"],
		row_count: [4, "dynamic"],
		headers: ["A", "B", "C", "D"],
		editable: true
	}}
	play={async ({ canvasElement }) => {
		const canvas = within(canvasElement);
		const cells = canvas.getAllByRole("cell");
		const user = userEvent.setup();

		// cmd+click to select non-contiguous cells
		await user.keyboard("[MetaLeft>]");
		await user.click(cells[4]);
		await user.click(cells[6]);
		await user.click(cells[2]);
		await user.keyboard("[/MetaLeft]");

		// shift+click to select a range
		await user.keyboard("[ShiftLeft>]");
		await user.click(cells[7]);
		await user.click(cells[6]);
		await user.keyboard("[/ShiftLeft]");

		// clear selected cells
		await user.keyboard("{Delete}");

		// verify cells were cleared by clicking one
		await user.click(cells[2]);
	}}
/>

<Story
	name="Dataframe toolbar interactions"
	args={{
		col_count: [3, "dynamic"],
		row_count: [2, "dynamic"],
		headers: ["Math", "Reading", "Writing"],
		values: [
			[800, 100, 400],
			[200, 800, 700]
		],
		show_fullscreen_button: true,
		show_copy_button: true
	}}
	play={async ({ canvasElement }) => {
		const canvas = within(canvasElement);

		const copy_button = canvas.getByRole("button", {
			name: "Copy table data"
		});
		await userEvent.click(copy_button);

		const fullscreen_button = canvas.getByRole("button", {
			name: "Fullscreen"
		});
		await userEvent.click(fullscreen_button);

		await userEvent.click(fullscreen_button);
	}}
/>

<Story
	name="Dataframe with row numbers"
	args={{
		values: [
			[95, 92, 88],
			[89, 90, 85],
			[92, 88, 91],
			[87, 85, 89],
			[91, 93, 90]
		],
		headers: ["Model A", "Model B", "Model C"],
		label: "Model Performance",
		col_count: [3, "dynamic"],
		row_count: [5, "dynamic"],
		show_row_numbers: true,
		editable: false
	}}
/>

<Story
	name="Dataframe with truncated text"
	args={{
		values: [
			[
				"This is a very long text that should be truncated",
				"Short text",
				"Another very long text that needs truncation"
			],
			[
				"Short",
				"This text is also quite long and should be truncated as well",
				"Medium length text here"
			],
			[
				"Medium text",
				"Brief",
				"This is the longest text in the entire table and it should definitely be truncated"
			]
		],
		headers: ["Column A", "Column B", "Column C"],
		label: "Truncated Text Example",
		max_chars: 20,
		col_count: [3, "dynamic"],
		row_count: [3, "dynamic"]
	}}
/>

<Story
	name="Dataframe with multiline headers"
	args={{
		values: [
			[95, 92, 88],
			[89, 90, 85],
			[92, 88, 91]
		],
		headers: [
			"Dataset A\nAccuracy",
			"Dataset B\nPrecision",
			"Dataset C\nRecall"
		],
		label: "Model Metrics",
		col_count: [3, "dynamic"],
		row_count: [3, "dynamic"],
		editable: false
	}}
/>

<Story
	name="Dataframe with custom components"
	args={{
		values: [
			[
				"Absol G",
				70,
				"https://images.pokemontcg.io/pl3/1_hires.png",
				"pl3-1",
				"Supreme Victors"
			]
		],
		datatype: ["str", "number", "image", "str", "str"],
		headers: ["Pokemon", "HP", "Image", "ID", "Set"],
		label: "Pokemon Cards",
		col_count: [5, "fixed"],
		row_count: [1, "dynamic"],
		editable: true,
		editable: true,
		components: {
			image: Image
		}
	}}
/>

<Story
	name="Dataframe with row and column selection"
	args={{
		values: [
			[1, 2, 3, 4],
			[5, 6, 7, 8],
			[9, 10, 11, 12],
			[13, 14, 15, 16]
		],
		col_count: [4, "dynamic"],
		row_count: [4, "dynamic"],
		headers: ["A", "B", "C", "D"],
		editable: true
	}}
	play={async ({ canvasElement }) => {
		const canvas = within(canvasElement);
		const user = userEvent.setup();

		const grid = canvas.getByRole("grid");
		await user.click(grid);

		const cells = canvas.getAllByRole("cell");
		await user.click(cells[5]); // Click cell with value 6

		const row_button = await canvas.findAllByRole("button", {
			name: "Select row"
		})[0];
		await user.click(row_button);

		await user.click(cells[6]);

		const col_button = await canvas.findAllByRole("button", {
			name: "Select column"
		})[0];
		await user.click(col_button);

		await user.keyboard("{Delete}");
	}}
/>

<Story
	name="Dataframe with lots of values"
	args={{
		values: [
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
			[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
		],
		col_count: [10, "dynamic"],
		row_count: [10, "dynamic"],
		max_height: 700
	}}
/>

<Story
	name="Dataframe with search and filter"
	args={{
		values: [
			["Cat", 5, "Pet"],
			["Horse", 3, "Farm"],
			["Snake", 1, "Pet"],
			["Cow", 4, "Farm"],
			["Dog", 6, "Pet"]
		],
		headers: ["Animal", "Count", "Type"],
		col_count: [3, "dynamic"],
		row_count: [5, "dynamic"],
		show_search: "filter",
		editable: false
	}}
	play={async ({ canvasElement }) => {
		const canvas = within(canvasElement);
		const user = userEvent.setup();

		const search_input = canvas.getByPlaceholderText("Filter...");
		await user.type(search_input, "Pet");
		await user.click(canvas.getByText("Cat"));

		await new Promise((resolve) => setTimeout(resolve, 100));

		const filter_button = canvas.getByLabelText(
			"Apply filter and update dataframe values"
		);
		await user.click(filter_button);

		await new Promise((resolve) => setTimeout(resolve, 100));
	}}
/>

<Story
	name="Dataframe with pinned columns"
	args={{
		values: [
			["ID", "Name", "Age", "City", "Country", "Score"],
			["1", "John", "25", "New York", "USA", "95"],
			["2", "Emma", "30", "London", "UK", "88"],
			["3", "Luis", "28", "Madrid", "Spain", "92"],
			["4", "Anna", "35", "Paris", "France", "90"],
			["5", "Chen", "27", "Beijing", "China", "94"]
		],
		headers: ["ID", "Name", "Age", "City", "Country", "Score"],
		label: "User Data",
		col_count: [6, "dynamic"],
		row_count: [6, "dynamic"],
		pinned_columns: 2,
		show_row_numbers: true,
		editable: false
	}}
/>

<Story
	name="Dataframe with pixel and percentage column widths set"
	args={{
		values: [
			[1, 2, 3, 4, 5],
			[6, 7, 8, 9, 10]
		],
		headers: ["10%", "50%", "40%", "100px", "100px"],
		col_count: [5, "dynamic"],
		row_count: [2, "dynamic"],
		column_widths: ["10%", "50%", "40%", "100px", "100px"]
	}}
/>

<Story
	name="Dataframe with drag selection"
	args={{
		values: [
			[1, 2, 3, 4, 5],
			[6, 7, 8, 9, 10],
			[11, 12, 13, 14, 15],
			[16, 17, 18, 19, 20],
			[21, 22, 23, 24, 25]
		],
		headers: ["A", "B", "C", "D", "E"],
		label: "Drag Selection Demo",
		col_count: [5, "dynamic"],
		row_count: [5, "dynamic"],
		editable: true
	}}
	play={async ({ canvasElement }) => {
		const canvas = within(canvasElement);
		const user = userEvent.setup();

		await new Promise((resolve) => setTimeout(resolve, 300));

		const table = canvas.getByRole("grid");
		const cells = canvas.getAllByRole("cell");
		const startCell = cells[6];
		const startRect = startCell.getBoundingClientRect();
		const startX = startRect.left + startRect.width / 2;
		const startY = startRect.top + startRect.height / 2;

		const endCell = cells[18];
		const endRect = endCell.getBoundingClientRect();
		const endX = endRect.left + endRect.width / 2;
		const endY = endRect.top + endRect.height / 2;

		await user.pointer({
			keys: "[MouseLeft>]",
			target: startCell,
			coords: { clientX: startX, clientY: startY }
		});

		await new Promise((resolve) => setTimeout(resolve, 50));

		const midX1 = startX + (endX - startX) / 3;
		const midY1 = startY + (endY - startY) / 3;
		await user.pointer({
			target: table,
			coords: { clientX: midX1, clientY: midY1 }
		});

		await new Promise((resolve) => setTimeout(resolve, 50));

		const midX2 = startX + ((endX - startX) * 2) / 3;
		const midY2 = startY + ((endY - startY) * 2) / 3;
		await user.pointer({
			target: table,
			coords: { clientX: midX2, clientY: midY2 }
		});

		await new Promise((resolve) => setTimeout(resolve, 50));

		await user.pointer({
			target: endCell,
			coords: { clientX: endX, clientY: endY }
		});

		await new Promise((resolve) => setTimeout(resolve, 50));

		await user.pointer({
			keys: "[/MouseLeft]"
		});

		await new Promise((resolve) => setTimeout(resolve, 500));
	}}
/>

<Story
	name="Non-interactive dataframe with sorting by multiple columns"
	args={{
		values: [
			[1, 2, 3],
			[4, 5, 6],
			[7, 8, 9]
		],
		headers: ["A", "B", "C"],
		col_count: [3, "dynamic"],
		row_count: [3, "dynamic"],
		editable: false,
		sort_columns: [
			{ col: 0, direction: "asc" },
			{ col: 1, direction: "desc" }
		],
		sort_state: {
			sort_columns: [
				{ col: 0, direction: "asc" },
				{ col: 1, direction: "desc" }
			],
			row_order: [0, 1, 2]
		}
	}}
	play={async ({ canvasElement }) => {
		const canvas = within(canvasElement);
		const user = userEvent.setup();

		const header_1 = canvas.getAllByText("A")[1];
		await userEvent.click(header_1);

		const cell_menu_button = canvas.getAllByLabelText("Open cell menu")[0];
		await userEvent.click(cell_menu_button);

		const sort_ascending_button = canvas.getByRole("menuitem", {
			name: "Sort ascending"
		});
		await userEvent.click(sort_ascending_button);

		const header_2 = canvas.getAllByText("B")[1];
		await userEvent.click(header_2);

		const cell_menu_button_2 = canvas.getAllByLabelText("Open cell menu")[1];
		await userEvent.click(cell_menu_button_2);

		const sort_descending_button = canvas.getByRole("menuitem", {
			name: "Sort descending"
		});
		await userEvent.click(sort_descending_button);

		const header_3 = canvas.getAllByText("C")[1];
		await userEvent.click(header_3);

		const cell_menu_button_3 = canvas.getAllByLabelText("Open cell menu")[2];
		await userEvent.click(cell_menu_button_3);

		const sort_ascending_button_3 = canvas.getByRole("menuitem", {
			name: "Sort ascending"
		});
		await userEvent.click(sort_ascending_button_3);

		await userEvent.click(header_3);
		await userEvent.click(cell_menu_button_3);
		await userEvent.click(canvas.getByText("Clear sort"));
	}}
/>

<Story
	name="Dataframe with display values"
	args={{
		values: [
			[95, 92, 88],
			[89, 90, 85],
			[92, 88, 91],
			[87, 85, 89],
			[91, 93, 90],
			[82, 81, 83]
		],
		headers: ["Model A", "Model B", "Empty Values"],
		display_value: [
			["🥇 95", "92", ""],
			["🥈 89", "90", ""],
			["🥉 92", "88", ""],
			["87", "85", ""],
			["91", "93", ""],
			["82", "81", ""]
		],
		label: "Model Performance with Medal Indicators",
		col_count: [3, "dynamic"],
		row_count: [6, "dynamic"],
		show_row_numbers: true,
		editable: false
	}}
/>

<Story
	name="Dataframe with text wrapping, no max chars"
	args={{
		values: [
			[
				"This is a very long text that should wrap to multiple lines when wrap is enabled",
				"Short text",
				"Another very long text that demonstrates wrapping behavior in the table cells"
			],
			[
				"Short",
				"This text is also quite long and should demonstrate the wrapping behavior when enabled",
				"Medium length text here"
			],
			[
				"Medium text",
				"Brief",
				"When wrap is disabled, this text should be truncated with an ellipsis instead of wrapping to multiple lines"
			]
		],
		headers: ["Column A", "Column B", "Column C"],
		label: "Text Wrapping Example",
		col_count: [3, "dynamic"],
		row_count: [3, "dynamic"],
		wrap: true,
		column_widths: ["33%", "33%", "33%"]
	}}
/>

<Story
	name="Dataframe text truncation and wrapping"
	args={{
		values: [
			[
				"This is a very long text that should be truncated with an ellipsis when wrap is false",
				"Short text",
				"Another very long text that should also be truncated with an ellipsis"
			],
			[
				"Short",
				"This text is also quite long and should be truncated with an ellipsis",
				"Medium length text here"
			],
			[
				"Medium text",
				"Brief",
				"When wrap is false this text should definitely be truncated with an ellipsis"
			]
		],
		headers: ["Column A", "Column B", "Column C"],
		label: "Text Truncation Example",
		col_count: [3, "dynamic"],
		row_count: [3, "dynamic"],
		wrap: true,
		max_chars: 50,
		editable: false,
		column_widths: ["200px", "200px", "200px"]
	}}
/>
