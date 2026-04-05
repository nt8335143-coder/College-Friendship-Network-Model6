# College-Friendship-Network-Model6
# import streamlit as st
# import networkx as nx
# import matplotlib.pyplot as plt
# import pandas as pd

# # ── Page Config ───────────────────────────────────────────────────────────────
# st.set_page_config(page_title="College Friendship Network", layout="wide")

# # ── Title & Description ───────────────────────────────────────────────────────
# st.title("College Friendship Network Model")
# st.write("""
# This application visualizes a simple **college friendship network** using Graph Theory.  
# Each node represents a student, and each edge represents a friendship between them.
# """)

# st.divider()

# # ── Session State ─────────────────────────────────────────────────────────────
# if "students" not in st.session_state:
#     st.session_state.students = []
# if "edges" not in st.session_state:
#     st.session_state.edges = []
# if "data_loaded" not in st.session_state:
#     st.session_state.data_loaded = False

# # ── Sidebar ───────────────────────────────────────────────────────────────────
# st.sidebar.header("Graph Controls")
# st.sidebar.divider()
# st.sidebar.header("Add New Student & Friendship")

# # ── Add Student ───────────────────────────────────────────────────────────────
# new_student = st.sidebar.text_input("New Student Name", placeholder="e.g. F")
# if st.sidebar.button("Add Student"):
#     name = new_student.strip().upper()
#     if not name:
#         st.sidebar.warning("Please enter a student name.")
#     elif name in st.session_state.students:
#         st.sidebar.warning(f"Student '{name}' already exists.")
#     else:
#         st.session_state.students.append(name)
#         st.session_state.data_loaded = True
#         st.sidebar.success(f"Student '{name}' added!")

# # ── Add Friendship ────────────────────────────────────────────────────────────
# st.sidebar.subheader("Add Friendship")
# s1 = st.sidebar.text_input("Student 1", placeholder="e.g. A")
# s2 = st.sidebar.text_input("Student 2", placeholder="e.g. F")
# if st.sidebar.button("Add Friendship"):
#     n1 = s1.strip().upper()
#     n2 = s2.strip().upper()
#     edge = tuple(sorted((n1, n2)))
#     if not n1 or not n2:
#         st.sidebar.warning("Please enter both student names.")
#     elif n1 == n2:
#         st.sidebar.warning("A student cannot be friends with themselves.")
#     elif n1 not in st.session_state.students or n2 not in st.session_state.students:
#         st.sidebar.warning("Both students must exist first.")
#     elif edge in [tuple(sorted(e)) for e in st.session_state.edges]:
#         st.sidebar.warning("This friendship already exists.")
#     else:
#         st.session_state.edges.append(edge)
#         st.session_state.data_loaded = True
#         st.sidebar.success(f"Friendship {n1} ↔ {n2} added!")

# # ── Reset ─────────────────────────────────────────────────────────────────────
# st.sidebar.divider()
# if st.sidebar.button("🔄 Reset"):
#     st.session_state.students   = []
#     st.session_state.edges      = []
#     st.session_state.data_loaded = False
#     st.rerun()

# # ══════════════════════════════════════════════════════════════════════════════
# # LANDING PAGE — shown before any data is loaded
# # ══════════════════════════════════════════════════════════════════════════════
# if not st.session_state.data_loaded:

#     st.markdown("""
#     ## 👋 Welcome to the College Friendship Network Model

#     This tool uses **Graph Theory** to model and analyze social connections between students in a college.

#     ---

#     ### 📖 How It Works

#     | Concept | Meaning |
#     |---|---|
#     | **Node** | A student in the college |
#     | **Edge** | A friendship between two students |
#     | **Degree Centrality** | How many direct friends a student has |
#     | **Betweenness Centrality** | How often a student acts as a bridge between others |
#     | **Closeness Centrality** | How quickly a student can reach everyone else |

#     ---

#     ### 🚀 Getting Started

#     You have two options:

#     - **Load Example Data** → instantly loads a sample 5-student network so you can explore all features right away.
#     - **Add manually** → use the sidebar on the left to add students and friendships one by one.

#     ---

#     ### 📊 What You Will See After Loading Data

#     - **Network Graph** — visual map of all student friendships
#     - **Centrality Analysis Table** — scores and roles for each student
#     - **Centrality Comparison Chart** — bar chart comparing all three centrality measures
#     - **Degree Distribution Chart** — how many friends each student has
#     - **Key Insights** — automatically identifies the most connected, key bridge, and fastest spreader
#     - **Network Summary Table** — density, connectivity, and average degree of the network

#     ---
#     """)

#     col_a, col_b, col_c = st.columns([1, 1, 1])
#     with col_b:
#         if st.button("📥 Load Example Data", use_container_width=True):
#             st.session_state.students    = ["A", "B", "C", "D", "E"]
#             st.session_state.edges       = [("A","B"), ("A","D"), ("B","C"), ("B","E"), ("D","E")]
#             st.session_state.data_loaded = True
#             st.rerun()

#     st.stop()

# # ══════════════════════════════════════════════════════════════════════════════
# # DATA IS LOADED — show everything below
# # ══════════════════════════════════════════════════════════════════════════════

# # ── Build Graph ───────────────────────────────────────────────────────────────
# G = nx.Graph()
# G.add_nodes_from(st.session_state.students)
# G.add_edges_from(st.session_state.edges)

# students = st.session_state.students
# edges    = st.session_state.edges

# # ── Load Example Data button (also available after loading) ───────────────────
# if st.button("📥 Load Example Data"):
#     st.session_state.students    = ["A", "B", "C", "D", "E"]
#     st.session_state.edges       = [("A","B"), ("A","D"), ("B","C"), ("B","E"), ("D","E")]
#     st.session_state.data_loaded = True
#     st.rerun()

# st.divider()

# # ══════════════════════════════════════════════════════════════════════════════
# # ROW 1 — Network Graph
# # ══════════════════════════════════════════════════════════════════════════════
# col1, col2 = st.columns([2, 1])

# with col1:
#     st.subheader("📊 Student Friendship Network")
#     fig, ax = plt.subplots(figsize=(4, 3))
#     pos = nx.spring_layout(G, seed=42)

#     nx.draw_networkx_nodes(G, pos, node_color='lightblue', node_size=1000, ax=ax)
#     nx.draw_networkx_edges(G, pos, width=2, ax=ax)
#     nx.draw_networkx_labels(G, pos, font_size=12, font_weight='bold', ax=ax)
#     ax.margins(0.1)
#     ax.set_title("Student Friendship Network", fontsize=14, fontweight='bold')
#     ax.axis('off')
#     st.pyplot(fig)


# st.divider()

# # ══════════════════════════════════════════════════════════════════════════════
# # ROW 2 — Centrality Table
# # ══════════════════════════════════════════════════════════════════════════════
# st.subheader("📐 Centrality Analysis")

# degree_cent = nx.degree_centrality(G)
# betweenness = nx.betweenness_centrality(G)
# closeness   = nx.closeness_centrality(G)

# centrality_df = pd.DataFrame({
#     "Student"                : list(degree_cent.keys()),
#     "Degree"                 : [G.degree(n) for n in degree_cent.keys()],
#     "Degree Centrality"      : [round(v, 3) for v in degree_cent.values()],
#     "Betweenness Centrality" : [round(betweenness[n], 3) for n in degree_cent.keys()],
#     "Closeness Centrality"   : [round(closeness[n], 3) for n in degree_cent.keys()],
# })

# max_deg = centrality_df["Degree Centrality"].max()
# max_bet = centrality_df["Betweenness Centrality"].max()
# max_clo = centrality_df["Closeness Centrality"].max()

# roles = []
# for _, row in centrality_df.iterrows():
#     role = []
#     if row["Degree Centrality"]      == max_deg: role.append("Most Connected")
#     if row["Betweenness Centrality"] == max_bet: role.append("Key Bridge")
#     if row["Closeness Centrality"]   == max_clo: role.append("Fast Spreader")
#     roles.append(", ".join(role) if role else "—")
# centrality_df["Role"] = roles

# st.dataframe(centrality_df, use_container_width=True)

# st.divider()

# # ══════════════════════════════════════════════════════════════════════════════
# # ROW 3 — Bar Chart + Degree Distribution Chart
# # ══════════════════════════════════════════════════════════════════════════════
# col3, col4 = st.columns(2)

# with col3:
#     st.subheader("📊 Centrality Comparison Chart")
#     plot_df = centrality_df.set_index("Student")
#     fig2, ax2 = plt.subplots(figsize=(6, 4))
#     plot_df[["Degree Centrality", "Betweenness Centrality", "Closeness Centrality"]].plot(
#         kind="bar", ax=ax2, colormap="Set2"
#     )
#     ax2.set_ylabel("Centrality Value")
#     ax2.set_title("Centrality Measures per Student")
#     ax2.set_xticklabels(plot_df.index, rotation=0)
#     ax2.legend(loc="upper right", fontsize=8)
#     ax2.set_ylim(0, 1.1)
#     st.pyplot(fig2)

# with col4:
#     degree_data = dict(G.degree())
#     st.subheader("📊 Degree Distribution Chart")
#     degree_df = pd.DataFrame({
#         "Student" : list(degree_data.keys()),
#         "Degree"  : list(degree_data.values())
#     }).sort_values("Degree", ascending=False)

#     fig3, ax3 = plt.subplots(figsize=(6, 4))
#     bars = ax3.bar(degree_df["Student"], degree_df["Degree"], color='lightblue', edgecolor="gray")
#     ax3.bar_label(bars, padding=3, fontsize=11, fontweight='bold')
#     ax3.set_xlabel("Student")
#     ax3.set_ylabel("Number of Friends")
#     ax3.set_title("Degree (Friendship Count) per Student")
#     ax3.set_ylim(0, max(degree_data.values()) + 1.5)
#     st.pyplot(fig3)

# st.divider()

# # ══════════════════════════════════════════════════════════════════════════════
# # ROW 4 — Key Insights
# # ══════════════════════════════════════════════════════════════════════════════
# col5, col6 = st.columns(2)

# with col5:
#     st.subheader("🔍 Key Insights")

#     top_degree      = centrality_df[centrality_df["Degree Centrality"]      == max_deg]["Student"].tolist()
#     top_betweenness = centrality_df[centrality_df["Betweenness Centrality"] == max_bet]["Student"].tolist()
#     top_closeness   = centrality_df[centrality_df["Closeness Centrality"]   == max_clo]["Student"].tolist()

#     st.success(f"🌟 Most Connected Student(s): {', '.join(top_degree)}")
#     st.info(f"🔗 Key Bridge Student(s): {', '.join(top_betweenness)}")
#     st.warning(f"⚡ Fast Information Spreader(s): {', '.join(top_closeness)}")

#     st.subheader("Observations")

#     top_deg_str = ", ".join([f"**{s}**" for s in top_degree])
#     max_friends = int(centrality_df["Degree"].max())
#     st.write(f"- Student {top_deg_str} has the highest number of connections ({max_friends} friends).")

#     if nx.is_connected(G):
#         st.write("- All students are interconnected, forming a fully connected social network.")
#     else:
#         st.write("- Some students are not connected to the main group — the network has isolated members.")

#     total_students    = G.number_of_nodes()
#     total_friendships = G.number_of_edges()
#     avg_deg = round(sum(dict(G.degree()).values()) / total_students, 2) if total_students > 0 else 0
#     st.write(f"- The network has **{total_students} students** and **{total_friendships} friendships**, with an average of **{avg_deg} friends** per student.")

#     bridge_str = ", ".join([f"**{s}**" for s in top_betweenness])
#     st.write(f"- Student {bridge_str} acts as the key bridge — removing them would most affect communication flow.")

#     density_val = round(nx.density(G), 3)
#     if density_val >= 0.6:
#         density_desc = "very dense — most students know each other"
#     elif density_val >= 0.3:
#         density_desc = "moderately connected"
#     else:
#         density_desc = "sparse — most students have few connections"
#     st.write(f"- This model helps understand how friendships are distributed (network density: **{density_val}** — {density_desc}).")

# st.divider()

# # ══════════════════════════════════════════════════════════════════════════════
# # ROW 5 — Network Summary Table
# # ══════════════════════════════════════════════════════════════════════════════
# st.subheader("📋 Network Summary")

# num_nodes    = G.number_of_nodes()
# num_edges    = G.number_of_edges()
# density      = round(nx.density(G), 3)
# is_connected = nx.is_connected(G)
# avg_degree   = round(sum(dict(G.degree()).values()) / num_nodes, 2) if num_nodes > 0 else 0

# summary_df = pd.DataFrame({
#     "Metric" : [
#         "Total Students (Nodes)",
#         "Total Friendships (Edges)",
#         "Network Density",
#         "Is Fully Connected?",
#         "Average Degree (Avg Friends)",
#     ],
#     "Value" : [
#         num_nodes,
#         num_edges,
#         density,
#         "Yes ✅" if is_connected else "No ❌",
#         avg_degree,
#     ]
# })

# st.table(summary_df)

# st.markdown("""
# ### What these measures mean:

# **Degree Centrality**  
# Shows how many direct friendships a student has. Higher value = more connected student.

# **Betweenness Centrality**  
# Shows how often a student acts as a bridge between others. High value = key connector in the network.

# **Closeness Centrality**  
# Measures how quickly a student can reach all others. Higher value = faster information spreading.

# These measures help identify **important students in the college friendship network.**
# """)

import streamlit as st
import networkx as nx
import matplotlib.pyplot as plt
import pandas as pd

# ── Page Config ───────────────────────────────────────────────────────────────
st.set_page_config(page_title="College Friendship Network", layout="wide")

# ── Title & Description ───────────────────────────────────────────────────────
st.title("College Friendship Network Model")
st.write("""
This application visualizes a simple **college friendship network** using Graph Theory.  
Each node represents a student, and each edge represents a friendship between them.
""")

st.divider()

# ── Session State ─────────────────────────────────────────────────────────────
if "students" not in st.session_state:
    st.session_state.students = []
if "edges" not in st.session_state:
    st.session_state.edges = []
if "data_loaded" not in st.session_state:
    st.session_state.data_loaded = False

# ── Sidebar ───────────────────────────────────────────────────────────────────
st.sidebar.header("Graph Controls")
st.sidebar.divider()
st.sidebar.header("Add New Student & Friendship")

# ── Add Student ───────────────────────────────────────────────────────────────
new_student = st.sidebar.text_input("New Student Name", placeholder="e.g. F")
if st.sidebar.button("Add Student"):
    name = new_student.strip().upper()
    if not name:
        st.sidebar.warning("Please enter a student name.")
    elif name in st.session_state.students:
        st.sidebar.warning(f"Student '{name}' already exists.")
    else:
        st.session_state.students.append(name)
        st.session_state.data_loaded = True
        st.sidebar.success(f"Student '{name}' added!")

# ── Add Friendship ────────────────────────────────────────────────────────────
st.sidebar.subheader("Add Friendship")
s1 = st.sidebar.text_input("Student 1", placeholder="e.g. A")
s2 = st.sidebar.text_input("Student 2", placeholder="e.g. F")
if st.sidebar.button("Add Friendship"):
    n1 = s1.strip().upper()
    n2 = s2.strip().upper()
    edge = tuple(sorted((n1, n2)))
    if not n1 or not n2:
        st.sidebar.warning("Please enter both student names.")
    elif n1 == n2:
        st.sidebar.warning("A student cannot be friends with themselves.")
    elif n1 not in st.session_state.students or n2 not in st.session_state.students:
        st.sidebar.warning("Both students must exist first.")
    elif edge in [tuple(sorted(e)) for e in st.session_state.edges]:
        st.sidebar.warning("This friendship already exists.")
    else:
        st.session_state.edges.append(edge)
        st.session_state.data_loaded = True
        st.sidebar.success(f"Friendship {n1} ↔ {n2} added!")

# ── Reset ─────────────────────────────────────────────────────────────────────
st.sidebar.divider()
if st.sidebar.button("🔄 Reset"):
    st.session_state.students    = []
    st.session_state.edges       = []
    st.session_state.data_loaded = False
    st.rerun()

# ══════════════════════════════════════════════════════════════════════════════
# LANDING PAGE — shown before any data is loaded
# ══════════════════════════════════════════════════════════════════════════════
if not st.session_state.data_loaded:

    st.markdown("""
    ## 👋 Welcome to the College Friendship Network Model

    This tool uses **Graph Theory** to model and analyze social connections between students in a college.

    ---

    ### 📖 How It Works

    | Concept | Meaning |
    |---|---|
    | **Node** | A student in the college |
    | **Edge** | A friendship between two students |
    | **Degree Centrality** | How many direct friends a student has |
    | **Betweenness Centrality** | How often a student acts as a bridge between others |
    | **Closeness Centrality** | How quickly a student can reach everyone else |

    ---

    ### 🚀 Getting Started

    You have two options:

    - **Load Example Data** → instantly loads a sample 5-student network so you can explore all features right away.
    - **Add manually** → use the sidebar on the left to add students and friendships one by one.

    ---

    ### 📊 What You Will See After Loading Data

    - **Network Graph** — visual map of all student friendships
    - **Centrality Analysis Table** — scores and roles for each student
    - **Centrality Comparison Chart** — bar chart comparing all three centrality measures
    - **Degree Distribution Chart** — how many friends each student has
    - **Key Insights** — automatically identifies the most connected, key bridge, and fastest spreader
    - **Network Summary Table** — density, connectivity, and average degree of the network

    ---
    """)

    col_a, col_b, col_c = st.columns([1, 1, 1])
    with col_b:
        if st.button("📥 Load Example Data", use_container_width=True):
            st.session_state.students    = ["A", "B", "C", "D", "E"]
            st.session_state.edges       = [("A","B"), ("A","D"), ("B","C"), ("B","E"), ("D","E")]
            st.session_state.data_loaded = True
            st.rerun()

    st.stop()

# ══════════════════════════════════════════════════════════════════════════════
# DATA IS LOADED — show everything below
# ══════════════════════════════════════════════════════════════════════════════

# ── Build Graph ───────────────────────────────────────────────────────────────
G = nx.Graph()
G.add_nodes_from(st.session_state.students)
G.add_edges_from(st.session_state.edges)

students = st.session_state.students
edges    = st.session_state.edges

if st.button("📥 Load Example Data"):
    st.session_state.students    = ["A", "B", "C", "D", "E"]
    st.session_state.edges       = [("A","B"), ("A","D"), ("B","C"), ("B","E"), ("D","E")]
    st.session_state.data_loaded = True
    st.rerun()

st.divider()

# ══════════════════════════════════════════════════════════════════════════════
# ROW 1 — Network Graph (full width)
# ══════════════════════════════════════════════════════════════════════════════
st.subheader("📊 Student Friendship Network")

fig, ax = plt.subplots(figsize=(10, 5))
pos = nx.spring_layout(G, seed=42)

nx.draw_networkx_nodes(G, pos, node_color='lightblue', node_size=2000, ax=ax)
nx.draw_networkx_edges(G, pos, width=2, ax=ax)
nx.draw_networkx_labels(G, pos, font_size=12, font_weight='bold', ax=ax)

ax.set_title("Student Friendship Network", fontsize=14, fontweight='bold')
ax.axis('off')
ax.margins(0.3)
st.pyplot(fig)

st.divider()

# ══════════════════════════════════════════════════════════════════════════════
# ROW 2 — Centrality Table
# ══════════════════════════════════════════════════════════════════════════════
st.subheader("📐 Centrality Analysis")

degree_cent = nx.degree_centrality(G)
betweenness = nx.betweenness_centrality(G)
closeness   = nx.closeness_centrality(G)

centrality_df = pd.DataFrame({
    "Student"                : list(degree_cent.keys()),
    "Degree"                 : [G.degree(n) for n in degree_cent.keys()],
    "Degree Centrality"      : [round(v, 3) for v in degree_cent.values()],
    "Betweenness Centrality" : [round(betweenness[n], 3) for n in degree_cent.keys()],
    "Closeness Centrality"   : [round(closeness[n], 3) for n in degree_cent.keys()],
})

max_deg = centrality_df["Degree Centrality"].max()
max_bet = centrality_df["Betweenness Centrality"].max()
max_clo = centrality_df["Closeness Centrality"].max()

roles = []
for _, row in centrality_df.iterrows():
    role = []
    if row["Degree Centrality"]      == max_deg: role.append("Most Connected")
    if row["Betweenness Centrality"] == max_bet: role.append("Key Bridge")
    if row["Closeness Centrality"]   == max_clo: role.append("Fast Spreader")
    roles.append(", ".join(role) if role else "—")
centrality_df["Role"] = roles

st.dataframe(centrality_df, use_container_width=True)

st.divider()

# ══════════════════════════════════════════════════════════════════════════════
# ROW 3 — Centrality Chart + Degree Distribution Chart
# ══════════════════════════════════════════════════════════════════════════════
col3, col4 = st.columns(2)

with col3:
    st.subheader("📊 Centrality Comparison Chart")
    plot_df = centrality_df.set_index("Student")
    fig2, ax2 = plt.subplots(figsize=(6, 4))
    plot_df[["Degree Centrality", "Betweenness Centrality", "Closeness Centrality"]].plot(
        kind="bar", ax=ax2, colormap="Set2"
    )
    ax2.set_ylabel("Centrality Value")
    ax2.set_title("Centrality Measures per Student")
    ax2.set_xticklabels(plot_df.index, rotation=0)
    ax2.legend(loc="upper right", fontsize=8)
    ax2.set_ylim(0, 1.1)
    st.pyplot(fig2)

with col4:
    degree_data = dict(G.degree())
    st.subheader("📊 Degree Distribution Chart")
    degree_df = pd.DataFrame({
        "Student" : list(degree_data.keys()),
        "Degree"  : list(degree_data.values())
    }).sort_values("Degree", ascending=False)

    fig3, ax3 = plt.subplots(figsize=(6, 4))
    bars = ax3.bar(degree_df["Student"], degree_df["Degree"], color='lightblue', edgecolor="gray")
    ax3.bar_label(bars, padding=3, fontsize=11, fontweight='bold')
    ax3.set_xlabel("Student")
    ax3.set_ylabel("Number of Friends")
    ax3.set_title("Degree (Friendship Count) per Student")
    ax3.set_ylim(0, max(degree_data.values()) + 1.5)
    st.pyplot(fig3)

st.divider()

# ══════════════════════════════════════════════════════════════════════════════
# ROW 4 — Key Insights (full width)
# ══════════════════════════════════════════════════════════════════════════════
st.subheader("🔍 Key Insights")

top_degree      = centrality_df[centrality_df["Degree Centrality"]      == max_deg]["Student"].tolist()
top_betweenness = centrality_df[centrality_df["Betweenness Centrality"] == max_bet]["Student"].tolist()
top_closeness   = centrality_df[centrality_df["Closeness Centrality"]   == max_clo]["Student"].tolist()

st.success(f"🌟 Most Connected Student(s): {', '.join(top_degree)}")
st.info(f"🔗 Key Bridge Student(s): {', '.join(top_betweenness)}")
st.warning(f"⚡ Fast Information Spreader(s): {', '.join(top_closeness)}")

st.subheader("Observations")

top_deg_str = ", ".join([f"**{s}**" for s in top_degree])
max_friends = int(centrality_df["Degree"].max())
st.write(f"- Student {top_deg_str} has the highest number of connections ({max_friends} friends).")

if nx.is_connected(G):
    st.write("- All students are interconnected, forming a fully connected social network.")
else:
    st.write("- Some students are not connected to the main group — the network has isolated members.")

total_students    = G.number_of_nodes()
total_friendships = G.number_of_edges()
avg_deg = round(sum(dict(G.degree()).values()) / total_students, 2) if total_students > 0 else 0
st.write(f"- The network has **{total_students} students** and **{total_friendships} friendships**, with an average of **{avg_deg} friends** per student.")

bridge_str = ", ".join([f"**{s}**" for s in top_betweenness])
st.write(f"- Student {bridge_str} acts as the key bridge — removing them would most affect communication flow.")

density_val = round(nx.density(G), 3)
if density_val >= 0.6:
    density_desc = "very dense — most students know each other"
elif density_val >= 0.3:
    density_desc = "moderately connected"
else:
    density_desc = "sparse — most students have few connections"
st.write(f"- This model helps understand how friendships are distributed (network density: **{density_val}** — {density_desc}).")

st.divider()

# ══════════════════════════════════════════════════════════════════════════════
# ROW 5 — Network Summary Table
# ══════════════════════════════════════════════════════════════════════════════
st.subheader("📋 Network Summary")

num_nodes    = G.number_of_nodes()
num_edges    = G.number_of_edges()
density      = round(nx.density(G), 3)
is_connected = nx.is_connected(G)
avg_degree   = round(sum(dict(G.degree()).values()) / num_nodes, 2) if num_nodes > 0 else 0

summary_df = pd.DataFrame({
    "Metric" : [
        "Total Students (Nodes)",
        "Total Friendships (Edges)",
        "Network Density",
        "Is Fully Connected?",
        "Average Degree (Avg Friends)",
    ],
    "Value" : [
        num_nodes,
        num_edges,
        density,
        "Yes ✅" if is_connected else "No ❌",
        avg_degree,
    ]
})

st.table(summary_df)

st.markdown("""
### What these measures mean:

**Degree Centrality**  
Shows how many direct friendships a student has. Higher value = more connected student.

**Betweenness Centrality**  
Shows how often a student acts as a bridge between others. High value = key connector in the network.

**Closeness Centrality**  
Measures how quickly a student can reach all others. Higher value = faster information spreading.

These measures help identify **important students in the college friendship network.**
""")
