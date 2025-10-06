<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Deadlock in Operating Systems</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
            color: #333;
        }
        header {
            background-color: #333;
            color: #fff;
            padding: 20px;
            text-align: center;
        }
        nav {
            background-color: #444;
            padding: 10px;
            text-align: center;
        }
        nav a {
            color: #fff;
            margin: 0 15px;
            text-decoration: none;
            font-weight: bold;
        }
        nav a:hover {
            color: #ddd;
        }
        main {
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
            background-color: #fff;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        section {
            margin-bottom: 40px;
        }
        h2 {
            color: #333;
            border-bottom: 2px solid #333;
            padding-bottom: 10px;
        }
        ul {
            margin: 10px 0;
            padding-left: 20px;
        }
        code {
            background-color: #f1f1f1;
            padding: 2px 4px;
            border-radius: 4px;
            font-family: monospace;
        }
        pre {
            background-color: #f1f1f1;
            padding: 10px;
            border-radius: 4px;
            overflow-x: auto;
        }
        footer {
            background-color: #333;
            color: #fff;
            text-align: center;
            padding: 10px;
            position: fixed;
            width: 100%;
            bottom: 0;
        }
    </style>
</head>
<body>
    <header>
        <h1>Deadlock in Operating Systems</h1>
        <p>An Educational Resource on Deadlocks</p>
    </header>

    <nav>
        <a href="#introduction">Introduction</a>
        <a href="#conditions">Conditions</a>
        <a href="#prevention">Prevention</a>
        <a href="#avoidance">Avoidance</a>
        <a href="#detection">Detection</a>
    </nav>

    <main>
        <section id="introduction">
            <h2>Introduction to Deadlock</h2>
            <p>A <strong>deadlock</strong> in an operating system occurs when two or more processes are unable to proceed because each is waiting for the other to release a resource. This creates a standstill where no process can make progress, leading to a system freeze or inefficiency.</p>
            <p>Deadlocks are common in multi-process environments, especially when processes share resources like files, memory, or hardware devices. Understanding deadlocks is crucial for OS design and resource management.</p>
            <p>Classic example: Two processes, P1 and P2, each holding a resource (e.g., tape drives) and waiting for the other's resource.</p>
        </section>

        <section id="conditions">
            <h2>Necessary Conditions for Deadlock</h2>
            <p>For a deadlock to occur, all four of the following conditions (Coffman conditions) must hold simultaneously:</p>
            <ul>
                <li><strong>Mutual Exclusion</strong>: At least one resource must be held in a non-sharable mode. Only one process can use it at a time.</li>
                <li><strong>Hold and Wait</strong>: A process holding at least one resource is waiting to acquire additional resources held by other processes.</li>
                <li><strong>No Preemption</strong>: Resources cannot be forcibly removed from a process; they must be released voluntarily.</li>
                <li><strong>Circular Wait</strong>: A set of processes form a circular chain, where each is waiting for a resource held by the next in the chain.</li>
            </ul>
            <p>If any one of these conditions is prevented, deadlock cannot occur.</p>
        </section>

        <section id="prevention">
            <h2>Deadlock Prevention</h2>
            <p>Deadlock prevention strategies aim to ensure that at least one of the four conditions cannot hold:</p>
            <ul>
                <li><strong>Eliminate Mutual Exclusion</strong>: Not always possible (e.g., printers can't be shared).</li>
                <li><strong>Eliminate Hold and Wait</strong>: Require processes to request all resources at once. If not available, release all and retry.</li>
                <li><strong>Allow Preemption</strong>: OS can preempt resources if needed, but this is complex for non-preemptible resources like I/O devices.</li>
                <li><strong>Prevent Circular Wait</strong>: Impose a total ordering on resources and require requests in increasing order (e.g., assign numbers to resources).</li>
            </ul>
            <p>These methods can be inefficient, as they may lead to low resource utilization.</p>
        </section>

        <section id="avoidance">
            <h2>Deadlock Avoidance</h2>
            <p>Instead of preventing deadlocks outright, avoidance dynamically checks if granting a resource request will lead to a safe state (one where deadlock is avoidable). Key algorithm:</p>
            <h3>Banker's Algorithm</h3>
            <p>Proposed by Dijkstra, it simulates resource allocation to ensure the system remains in a safe state.</p>
            <ul>
                <li><strong>Available</strong>: Vector of free resources.</li>
                <li><strong>Max</strong>: Maximum resources each process may need.</li>
                <li><strong>Allocation</strong>: Currently allocated resources per process.</li>
                <li><strong>Need</strong>: Remaining resources each process needs (Need = Max - Allocation).</li>
            </ul>
            <p>The algorithm checks if, after allocation, there exists a safe sequence of processes that can finish without deadlock.</p>
            <pre><code>// Pseudocode for Safety Check in Banker's Algorithm
bool isSafe() {
    int work[n] = available;  // n = number of resource types
    bool finish[m] = false;   // m = number of processes
    
    while (true) {
        bool found = false;
        for (int i = 0; i < m; i++) {
            if (!finish[i] && need[i] <= work) {
                work += allocation[i];
                finish[i] = true;
                found = true;
            }
        }
        if (!found) break;
    }
    
    for (int i = 0; i < m; i++) {
        if (!finish[i]) return false;
    }
    return true;
}
</code></pre>
            <p>This requires advance knowledge of maximum needs, which may not always be feasible.</p>
        </section>

        <section id="detection">
            <h2>Deadlock Detection and Recovery</h2>
            <p>Detection periodically checks for deadlocks using algorithms like resource allocation graphs (RAG). In a RAG:</p>
            <ul>
                <li>Nodes: Processes (circles) and Resources (squares).</li>
                <li>Edges: Request (process to resource) or Assignment (resource to process).</li>
                <li>Deadlock if there's a cycle in the graph for single-instance resources.</li>
            </ul>
            <p>For multiple instances, use reduction algorithms similar to Banker's.</p>
            <p><strong>Recovery Options</strong>:</p>
            <ul>
                <li>Terminate processes and rollback.</li>
                <li>Preempt resources and rollback affected processes.</li>
                <li>Choose victim based on priority, cost, etc., to minimize damage.</li>
            </ul>
            <p>Detection is resource-intensive, so it's used sparingly (e.g., every few minutes).</p>
        </section>
    </main>

    <footer>
        <p>&copy; 2023 Educational Website on OS Deadlocks. For learning purposes only.</p>
    </footer>
</body>
</html>
