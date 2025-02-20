<%*
const dv = app.plugins.plugins["dataview"].api;

// Your exact dataview query
const query = `list rows.file.link
from [[]]
group by status`;

// Get the query output in markdown format
const queryOutput = await dv.queryMarkdown(query);

// Write the result to the current file
tR += queryOutput.value;
%>