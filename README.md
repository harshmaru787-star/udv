import React, { useState } from "react";
import { BarChart, Bar, XAxis, YAxis, Tooltip, CartesianGrid } from "recharts";
import dayjs from "dayjs";

export default function App() {
  const [tasks, setTasks] = useState([
    { id: 1, name: "Site Preparation", start: "2025-10-01", end: "2025-10-05" },
    { id: 2, name: "Foundation Work", start: "2025-10-06", end: "2025-10-15" },
    { id: 3, name: "Framing", start: "2025-10-16", end: "2025-10-25" },
  ]);

  const [newTask, setNewTask] = useState({ name: "", start: "", end: "" });

  // Convert tasks into chart format
  const data = tasks.map((t) => ({
    name: t.name,
    start: dayjs(t.start).valueOf(),
    end: dayjs(t.end).valueOf(),
    duration: dayjs(t.end).diff(dayjs(t.start), "day"),
  }));

  const addTask = () => {
    if (!newTask.name || !newTask.start || !newTask.end) return;
    setTasks([...tasks, { ...newTask, id: tasks.length + 1 }]);
    setNewTask({ name: "", start: "", end: "" });
  };

  return (
    <div style={{ padding: 20 }}>
      <h1>🏗 Construction Gantt Chart</h1>

      {/* Add New Task Form */}
      <div style={{ marginBottom: 20 }}>
        <input
          placeholder="Task Name"
          value={newTask.name}
          onChange={(e) => setNewTask({ ...newTask, name: e.target.value })}
        />
        <input
          type="date"
          value={newTask.start}
          onChange={(e) => setNewTask({ ...newTask, start: e.target.value })}
        />
        <input
          type="date"
          value={newTask.end}
          onChange={(e) => setNewTask({ ...newTask, end: e.target.value })}
        />
        <button onClick={addTask}>Add Task</button>
      </div>

      {/* Gantt Chart */}
      <BarChart
        width={900}
        height={400}
        data={data}
        layout="vertical"
        margin={{ left: 100 }}
      >
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis
          type="number"
          domain={["dataMin", "dataMax"]}
          tickFormatter={(time) => dayjs(time).format("MMM DD")}
        />
        <YAxis dataKey="name" type="category" />
        <Tooltip
          labelFormatter={(label) => `Task: ${label}`}
          formatter={(value, name, props) =>
            `${dayjs(props.payload.start).format("MMM DD")} - ${dayjs(
              props.payload.end
            ).format("MMM DD")}`
          }
        />
        <Bar
          dataKey="duration"
          fill="#4CAF50"
          background={{ fill: "#eee" }}
          label={{ position: "insideRight" }}
        />
      </BarChart>
    </div>
  );
}
