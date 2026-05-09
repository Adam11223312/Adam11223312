"use client";

import { useEffect, useState } from "react";

export default function DavidSaaS() {
  const [logs, setLogs] = useState<any[]>([]);
  const [decision, setDecision] = useState("");
  const [loading, setLoading] = useState(false);

  // -------------------------------
  // LOCAL POLICY ENGINE
  // -------------------------------
  function evaluateAction(action: any) {
    if (action.type === "finance" && action.amount > 100) {
      return {
        status: "REQUIRES_APPROVAL",
        reason: "Amount exceeds approval limit"
      };
    }

    if (action.type === "vehicle") {
      return {
        status: "REQUIRES_APPROVAL",
        reason: "Vehicle actions require authorization"
      };
    }

    return {
      status: "ALLOWED"
    };
  {

  // -------------------------------
  // RUN ACTION
  // -------------------------------
  async function runAction(action: any) {
    setLoading(true);

    try {
      const result = evaluateAction(action);

      const log = {
        action,
        decision: result,
        timestamp: new Date().toLocaleString()
      };

      const updatedLogs = [log, ...logs];

      setLogs(updatedLogs);

      localStorage.setItem(
        "david_logs",
        JSON.stringify(updatedLogs)
      );

      setDecision(JSON.stringify(result, null, 2));

    } catch (error: any) {
      setDecision(error.message);
    }

    setLoading(false);
  }

  // -------------------------------
  // LOAD LOGS
  // -------------------------------
  useEffect(() => {
    const saved = localStorage.getItem("david_logs");

    if (saved) {
      setLogs(JSON.parse(saved));
    }
  }, []);

  // -------------------------------
  // CLEAR LOGS
  // -------------------------------
  function clearLogs() {
    localStorage.removeItem("david_logs");
    setLogs([]);
  }

  // -------------------------------
  // UI
  // -------------------------------
  return (
    <main
      style={{
        minHeight: "100vh",
        background: "#0b1020",
        color: "white",
        padding: 40,
        fontFamily: "Arial"
      }}
    >
      {/* HEADER */}
      <div
        style={{
          marginBottom: 40
        }}
      >
        <h1
          style={{
            fontSize: 48,
            marginBottom: 10
          }}
        >
          DAVID SaaS
        </h1>

        <h2
          style={{
            color: "#00d4ff",
            marginBottom: 20
          }}
        >
          AI Governance & Action Control
        </h2>

        <p
          style={{
            maxWidth: 700,
            lineHeight: 1.6
          }}
        >
          DAVID evaluates, approves, blocks, and audits
          AI-driven actions across enterprise systems.
        </p>
      </div>

      {/* ACTION BUTTONS */}
      <div
        style={{
          display: "flex",
          gap: 15,
          flexWrap: "wrap",
          marginBottom: 30
        }}
      >
        <button
          onClick={() =>
            runAction({
              type: "finance",
              amount: 150
            })
          }
          disabled={loading}
          style={buttonStyle}
        >
          Run Finance Action
        </button>

        <button
          onClick={() =>
            runAction({
              type: "finance",
              amount: 50
            })
          }
          disabled={loading}
          style={buttonStyle}
        >
          Small Finance Action
        </button>

        <button
          onClick={() =>
            runAction({
              type: "vehicle"
            })
          }
          disabled={loading}
          style={buttonStyle}
        >
          Vehicle Action
        </button>

        <button
          onClick={() =>
            runAction({
              type: "ai_agent"
            })
          }
          disabled={loading}
          style={buttonStyle}
        >
          AI Agent Action
        </button>

        <button
          onClick={clearLogs}
          style={{
            ...buttonStyle,
            background: "#aa2222"
          }}
        >
          Clear Logs
        </button>
      </div>

      {/* DECISION PANEL */}
      <section
        style={{
          background: "#111827",
          padding: 25,
          borderRadius: 16,
          marginBottom: 30,
          border: "1px solid #1f2937"
        }}
      >
        <h2
          style={{
            marginBottom: 15
          }}
        >
          Latest Decision
        </h2>

        <pre
          style={{
            color: "#00ff99",
            whiteSpace: "pre-wrap"
          }}
        >
          {decision || "No actions yet"}
        </pre>
      </section>

      {/* AUDIT LOGS */}
      <section
        style={{
          background: "#111827",
          padding: 25,
          borderRadius: 16,
          border: "1px solid #1f2937"
        }}
      >
        <h2
          style={{
            marginBottom: 20
          }}
        >
          Audit Logs
        </h2>

        {logs.length === 0 ? (
          <p>No logs recorded.</p>
        ) : (
          logs.map((log, index) => (
            <div
              key={index}
              style={{
                background: "#1f2937",
                padding: 16,
                borderRadius: 12,
                marginBottom: 12
              }}
            >
              <div>
                <strong>Timestamp:</strong>{" "}
                {log.timestamp}
              </div>

              <div>
                <strong>Action:</strong>{" "}
                {JSON.stringify(log.action)}
              </div>

              <div>
                <strong>Status:</strong>{" "}
                {log.decision.status}
              </div>

              {log.decision.reason && (
                <div>
                  <strong>Reason:</strong>{" "}
                  {log.decision.reason}
                </div>
              )}
            </div>
          ))
        )}
      </section>
    </main>
  );
}

// -------------------------------
// BUTTON STYLE
// -------------------------------
const buttonStyle: React.CSSProperties = {
  padding: "12px 20px",
  borderRadius: 10,
  border: "none",
  background: "#0066ff",
  color: "white",
  cursor: "pointer",
  fontSize: 14,
  fontWeight: "bold"
};
