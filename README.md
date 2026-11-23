# Bus445-term-group-project-
from pathlib import Path
import argparse, csv, json, platform
from collections import deque
from decimal import Decimal, ROUND_HALF_EVEN
import numpy as np
import networkx as nx
import matplotlib
matplotlib.use("Agg")
import matplotlib.pyplot as plt
from IPython.display import Image, display
ROOT = Path.cwd()
def save_csv(path, rows):
    rows = list(rows)
    with path.open('w', newline='', encoding='utf-8') as f:
        w = csv.DictWriter(f, fieldnames=list(rows[0])); w.writeheader(); w.writerows(rows)
def load_graphs():
    rows = [{'participant': 'P08', 'ai_use_frequency': 'Several times a week', 'ai_tools_used': 'ChatGPT, Google Gemini', 'ai_discussion_frequency': 'About once a week', 'sharing_frequency': 'Sometimes', 'recommendation_likelihood': 'Likely', 'discovery_sources': 'Friends or classmates, YouTube or other online videos, social media', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'Yes', 'contacts': 'P07, P04, P10'}, {'participant': 'P07', 'ai_use_frequency': 'Several times a week', 'ai_tools_used': 'ChatGPT, Google Gemini', 'ai_discussion_frequency': 'Rarely', 'sharing_frequency': 'Rarely', 'recommendation_likelihood': 'Likely', 'discovery_sources': 'Internet searches or websites, social media, My dad', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'no', 'contacts': 'P08, P09, P14'}, {'participant': 'P09', 'ai_use_frequency': 'Always', 'ai_tools_used': 'ChatGPT, Microsoft Copilot, Google Gemini, Claude, Other AI tools', 'ai_discussion_frequency': 'Every day', 'sharing_frequency': 'Very often', 'recommendation_likelihood': 'Very likely', 'discovery_sources': 'Friends or classmates, Professors or instructors, YouTube or other online videos, Internet searches or websites, social media', 'try_if_trusted_recommendation': 'Very likely', 'previously_recommended': 'Yes', 'contacts': 'P07, P30, P20, P23'}, {'participant': 'P11', 'ai_use_frequency': 'Rarely', 'ai_tools_used': 'ChatGPT', 'ai_discussion_frequency': 'Rarely', 'sharing_frequency': 'Rarely', 'recommendation_likelihood': 'Unlikely', 'discovery_sources': 'Friends or classmates, YouTube or other online videos, social media', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'Yes', 'contacts': 'P12'}, {'participant': 'P01', 'ai_use_frequency': 'Several times a week', 'ai_tools_used': 'ChatGPT, Microsoft Copilot', 'ai_discussion_frequency': 'Rarely', 'sharing_frequency': 'Never', 'recommendation_likelihood': 'Likely', 'discovery_sources': 'Friends or classmates, Internet searches or websites, social media', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'Yes', 'contacts': 'P04'}, {'participant': 'P12', 'ai_use_frequency': 'Several times a week', 'ai_tools_used': 'ChatGPT', 'ai_discussion_frequency': 'About once a week', 'sharing_frequency': 'Sometimes', 'recommendation_likelihood': 'Unlikely', 'discovery_sources': 'Friends or classmates, YouTube or other online videos, social media', 'try_if_trusted_recommendation': 'Very likely', 'previously_recommended': 'Yes', 'contacts': 'P11, P21'}, {'participant': 'P14', 'ai_use_frequency': 'Several times a week', 'ai_tools_used': 'ChatGPT', 'ai_discussion_frequency': 'Rarely', 'sharing_frequency': 'Rarely', 'recommendation_likelihood': 'Unlikely', 'discovery_sources': 'Internet searches or websites, social media', 'try_if_trusted_recommendation': 'Unlikely', 'previously_recommended': 'never', 'contacts': 'P07'}, {'participant': 'P04', 'ai_use_frequency': 'Every day', 'ai_tools_used': 'ChatGPT, Google Gemini, Claude', 'ai_discussion_frequency': 'Rarely', 'sharing_frequency': 'Sometimes', 'recommendation_likelihood': 'Likely', 'discovery_sources': 'Friends or classmates, social media', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'Yes', 'contacts': 'P08, P01, P10'}, {'participant': 'P30', 'ai_use_frequency': 'Several times a week', 'ai_tools_used': 'ChatGPT', 'ai_discussion_frequency': 'Several times a week', 'sharing_frequency': 'Often', 'recommendation_likelihood': 'Very likely', 'discovery_sources': 'Friends or classmates, Internet searches or websites', 'try_if_trusted_recommendation': 'Very likely', 'previously_recommended': 'Yes', 'contacts': 'P09, P23, P20, P10'}, {'participant': 'P10', 'ai_use_frequency': 'Several times a week', 'ai_tools_used': 'ChatGPT, Microsoft Copilot, Other AI tools', 'ai_discussion_frequency': 'Rarely', 'sharing_frequency': 'Sometimes', 'recommendation_likelihood': 'Very likely', 'discovery_sources': 'Friends or classmates, Professors or instructors, YouTube or other online videos, social media', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'Yes', 'contacts': 'P01'}, {'participant': 'P29', 'ai_use_frequency': 'Every day', 'ai_tools_used': 'ChatGPT', 'ai_discussion_frequency': 'Rarely', 'sharing_frequency': 'Rarely', 'recommendation_likelihood': 'Likely', 'discovery_sources': 'Friends or classmates, YouTube or other online videos, Internet searches or websites, social media', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'Yes', 'contacts': 'Nobody'}, {'participant': 'P28', 'ai_use_frequency': 'About once a week', 'ai_tools_used': 'ChatGPT, Microsoft Copilot', 'ai_discussion_frequency': 'Never', 'sharing_frequency': 'Sometimes', 'recommendation_likelihood': 'Likely', 'discovery_sources': 'Friends or classmates, Professors or instructors, YouTube or other online videos, Internet searches or websites, social media,', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'no', 'contacts': 'P09, P30, P23, P22'}, {'participant': 'P20', 'ai_use_frequency': 'Several times a week', 'ai_tools_used': 'ChatGPT, Microsoft Copilot, Google Gemini, Claude, Other AI tools', 'ai_discussion_frequency': 'Several times a week', 'sharing_frequency': 'Sometimes', 'recommendation_likelihood': 'Likely', 'discovery_sources': 'Friends or classmates, Professors or instructors, YouTube or other online videos, Internet searches or websites, social media', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'Yes', 'contacts': 'P12, P22'}, {'participant': 'P21', 'ai_use_frequency': 'About once a week', 'ai_tools_used': 'ChatGPT', 'ai_discussion_frequency': 'About once a week', 'sharing_frequency': 'Often', 'recommendation_likelihood': 'Unlikely', 'discovery_sources': 'Friends or classmates', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'no', 'contacts': 'P20, P25'}, {'participant': 'P22', 'ai_use_frequency': 'About once a week', 'ai_tools_used': 'ChatGPT', 'ai_discussion_frequency': 'About once a week', 'sharing_frequency': 'Sometimes', 'recommendation_likelihood': 'Likely', 'discovery_sources': 'Friends or classmates, Internet searches or websites', 'try_if_trusted_recommendation': 'Unlikely', 'previously_recommended': 'no', 'contacts': 'P09, P21, P25'}, {'participant': 'P23', 'ai_use_frequency': 'Every day', 'ai_tools_used': 'ChatGPT, Microsoft Copilot, Google Gemini, Claude, Other AI tools', 'ai_discussion_frequency': 'Several times a week', 'sharing_frequency': 'Often', 'recommendation_likelihood': 'Very likely', 'discovery_sources': 'Friends or classmates, Professors or instructors, YouTube or other online videos, Internet searches or websites, social media', 'try_if_trusted_recommendation': 'Likely', 'previously_recommended': 'Yes', 'contacts': 'P09, P30, P20, P25'}, {'participant': 'P25', 'ai_use_frequency': 'Rarely', 'ai_tools_used': 'ChatGPT, Microsoft Copilot, Google Gemini, Claude, Other AI tools', 'ai_discussion_frequency': 'About once a week', 'sharing_frequency': 'Often', 'recommendation_likelihood': 'Unlikely', 'discovery_sources': 'social media', 'try_if_trusted_recommendation': 'Very unlikely', 'previously_recommended': 'Yes', 'contacts': 'P22, P23'}, {'participant': 'P26', 'ai_use_frequency': 'Never', 'ai_tools_used': 'I have not used AI tools', 'ai_discussion_frequency': 'Never', 'sharing_frequency': 'Never', 'recommendation_likelihood': 'Very unlikely', 'discovery_sources': 'Friends or classmates', 'try_if_trusted_recommendation': 'Very unlikely', 'previously_recommended': 'no', 'contacts': 'Nobody'}]
    ids = [r['participant'] for r in rows]
    if len(ids) != len(set(ids)): raise ValueError('Duplicate participant IDs')
    import re
    nominations = {(r['participant'], p) for r in rows for p in re.findall(r'P\d+', r['contacts'])}
    if any(b not in ids or a == b for a,b in nominations): raise ValueError('Unknown contact or self nomination')
    graphs = {}
    for name in ['either_report','mutual_report']:
        g = nx.Graph(); g.add_nodes_from(sorted(ids))
        edges = {tuple(sorted((a,b))) for a,b in nominations if name=='either_report' or (b,a) in nominations}
        g.add_edges_from(sorted(edges)); graphs[name] = g
    return rows, nominations, graphs

rows, nominations, graphs = load_graphs()
for name, graph in graphs.items():
    print(name, "participants:", len(graph), "connections:", graph.number_of_edges())
print("Directed nominations:", len(nominations))
def metrics(g):
    nodes = sorted(g); a = nx.to_numpy_array(g,nodelist=nodes)
    # Symmetric eigensolver; disconnected graphs need a unique dominant eigenvalue.
    vals, vecs = np.linalg.eigh(a)
    if np.isclose(vals[-1], vals[-2], atol=1e-10):
        raise ValueError('Nonunique dominant eigenvalue; choose an explicit eigenvector convention')
    eig = np.abs(vecs[:,-1]); eig[eig<1e-12] = 0
    deg = nx.degree_centrality(g)
    bet = nx.betweenness_centrality(g,normalized=True,endpoints=False)
    return {n:{'participant':n,'degree':g.degree(n),'degree_centrality':deg[n],
               'betweenness':bet[n],'eigenvector':float(eig[i])} for i,n in enumerate(nodes)}

def select_seeds(m, key, k=3):
    # Treat numerical differences under 1e-12 as ties; break ties by participant ID.
    return sorted(m,key=lambda n:(-round(m[n][key],12),n))[:k]

for name in ["mutual_report", "either_report"]:
    scores = metrics(graphs[name])
    print("\n", name)
    print("Participant  Connections  Degree  Betweenness  Eigenvector")
    for node, values in scores.items():
        print(node, values["degree"], *[f"{values[k]:.4f}" for k in ["degree_centrality", "betweenness", "eigenvector"]])
    for measure in ["degree_centrality", "betweenness", "eigenvector"]:
        print(measure, select_seeds(scores, measure))



def cascade(live, seeds):
    """Synchronous reach on a sampled live-edge graph, equivalent to IC.
    Every directed contact gets an independent Bernoulli trial; each active node
    gets only one attempt per neighbor. Newly reached nodes transmit next round.
    """
    reached=set(seeds); frontier=set(seeds); history=[sorted(reached)]
    while frontier:
        new={v for u in frontier for v in live[u]}-reached
        if not new: break
        reached |= new; frontier=new; history.append(sorted(reached))
    return len(reached), len(history)-1, history

def live_graph(nodes, arcs, uniforms, p):
    live={n:[] for n in nodes}
    for (a,b),u in zip(arcs,uniforms):
        if u<p:live[a].append(b)
    return live

def checks():
    # Model invariants, independent of the survey and centrality implementation.
    nodes=['A','B','C']; arcs=[('A','B'),('B','A'),('B','C'),('C','B')]
    assert cascade(live_graph(nodes,arcs,[.5]*4,0),['A'])[:2]==(1,0)
    assert cascade(live_graph(nodes,arcs,[.5]*4,1),['A'])[:2]==(3,2)
    assert cascade({'A':[],'B':[]},['A'])[:2]==(1,0)
    assert cascade({'A':['B','C'],'B':['C'],'C':[]},['A'])[0]==3

checks()
print("Simulation checks passed")
def main():
    parser=argparse.ArgumentParser()
    parser.add_argument('--runs',type=int,default=10000)
    parser.add_argument('--seed',type=int,default=4452026)
    parser.add_argument('--demo',action='store_true')
    parser.add_argument('--seeds',default='P09,P20,P22')
    parser.add_argument('--prob',type=float,default=.25)
    parser.add_argument('--network',choices=['either_report','mutual_report'],default='either_report')
    args=parser.parse_args([])
    if args.runs<2 or not 0<=args.prob<=1:parser.error('runs >= 2 and 0 <= prob <= 1 required')
    checks(); rows,nominations,graphs=load_graphs(); out=ROOT/'results';out.mkdir(exist_ok=True)
    if args.demo:
        g=graphs[args.network]; seeds=args.seeds.split(',')
        if len(seeds)!=len(set(seeds)) or not set(seeds)<=set(g):parser.error('Use distinct existing participant IDs')
        nodes=sorted(g);arcs=sorted((a,b) for a in nodes for b in g.neighbors(a))
        rng=np.random.default_rng(args.seed)
        reach,rounds,history=cascade(live_graph(nodes,arcs,rng.random(len(arcs)),args.prob),seeds)
        print(f'Loaded {len(rows)} responses; {g.number_of_edges()} edges; p={args.prob}; seeds={seeds}')
        for t, reached in enumerate(history):print(f'Round {t}: {reached}')
        print(f'Final reach: {reach}/{len(g)} ({100*reach/len(g):.1f}%); successful rounds={rounds}')
        return
    summary=[]; all_central=[]; simulations=[]; differences=[]; network_stats={};seedsets={}
    for ni,(name,g) in enumerate(graphs.items()):
        nodes=sorted(g); m=metrics(g)
        central=[dict(network=name,**m[n]) for n in nodes]; all_central.extend(central)
        sets={key:select_seeds(m,key) for key in ['degree_centrality','betweenness','eigenvector']}
        seedsets[name]=sets
        comps=sorted(nx.connected_components(g),key=lambda c:(-len(c),sorted(c)))
        network_stats[name]={'nodes':len(g),'edges':g.number_of_edges(),'density':nx.density(g),
           'average_degree':sum(dict(g.degree()).values())/len(g),'components':[sorted(c) for c in comps],
           'isolates':sorted(nx.isolates(g)),'dominant_eigenvalue':float(np.linalg.eigvalsh(nx.to_numpy_array(g))[-1])}
        save_csv(out/f'{name}_edges.csv',[{'source':a,'target':b} for a,b in sorted(g.edges())])
        arcs=sorted((a,b) for a in nodes for b in g.neighbors(a))
        for pi,p in enumerate([.1,.25,.5]):
            # Separate reproducible streams for transmission trials and random seeds.
            tx=np.random.default_rng(args.seed+ni*1000+pi*10)
            rs=np.random.default_rng(args.seed+ni*1000+pi*10+1)
            vals={s:[] for s in [*sets,'random']}; rounds={s:[] for s in vals}
            for run in range(args.runs):
                live=live_graph(nodes,arcs,tx.random(len(arcs)),p)
                choices={**sets,'random':sorted(rs.choice(nodes,size=3,replace=False).tolist())}
                for strategy,seeds in choices.items():
                    reach,t,_=cascade(live,seeds); vals[strategy].append(reach);rounds[strategy].append(t)
                    simulations.append({'network':name,'probability':p,'run':run,'strategy':strategy,
                        'seeds':','.join(seeds),'reach':reach,'successful_rounds':t})
            baseline=np.asarray(vals['random'])
            for strategy,v in vals.items():
                v=np.asarray(v);sd=float(v.std(ddof=1));mc=1.96*sd/np.sqrt(args.runs)
                summary.append({'network':name,'probability':p,'strategy':strategy,'runs':args.runs,
                    'mean_reach':float(v.mean()),'reach_percent':float(v.mean()/len(g)*100),
                    'mean_additional':float(v.mean()-3),'sd_reach':sd,'mc95_low':float(v.mean()-mc),
                    'mc95_high':float(v.mean()+mc),'mean_successful_rounds':float(np.mean(rounds[strategy]))})
                if strategy!='random':
                    diff=v-baseline;d=float(diff.mean());se=float(diff.std(ddof=1)/np.sqrt(args.runs))
                    differences.append({'network':name,'probability':p,'strategy':strategy,
                        'additional_reach_vs_random':d,'percentage_point_gain':d/len(g)*100,
                        'relative_gain_percent':d/float(baseline.mean())*100,'paired_mc95_low':d-1.96*se,
                        'paired_mc95_high':d+1.96*se})
        # Shared node positions across edge definitions support visual comparison.
        if ni==0:
            main_nodes=max(nx.connected_components(g),key=len)
            pos=nx.spring_layout(g.subgraph(main_nodes),seed=445,iterations=150)
            for j,n in enumerate(sorted(set(g)-main_nodes)):
                pos[n]=np.array([1.65,0.65-j*1.3])
        fig,ax=plt.subplots(figsize=(8,5.3));ax.axis('off')
        colors=['#B7C4CD' if g.degree(n)==0 else '#397694' for n in nodes]
        nx.draw_networkx(g,pos=pos,ax=ax,nodelist=nodes,node_color=colors,
           node_size=[480+100*g.degree(n) for n in nodes],font_color='white',font_size=9,
           edge_color='#ABB8C0',width=1.5)
        fig.tight_layout();fig.savefig(out/f'{name}_network.png',dpi=180);plt.close(fig)
    save_csv(out/'centrality.csv',all_central);save_csv(out/'simulation_summary.csv',summary)
    save_csv(out/'paired_comparisons.csv',differences);save_csv(out/'simulation_runs.csv',simulations)
    meta={'random_seed':args.seed,'runs_per_configuration':args.runs,'nominations':len(nominations),
       'network_stats':network_stats,'seed_sets':seedsets,'python':platform.python_version(),
       'numpy':np.__version__,'networkx':nx.__version__}
    (out/'metadata.json').write_text(json.dumps(meta,indent=2))
    for name in graphs:
        fig,ax=plt.subplots(figsize=(7.5,4));x=np.arange(3);width=.19
        labels={'degree_centrality':'Degree','betweenness':'Betweenness','eigenvector':'Eigenvector','random':'Random'}
        for i,(strategy,label) in enumerate(labels.items()):
            r=[r for r in summary if r['network']==name and r['strategy']==strategy]
            ax.bar(x+(i-1.5)*width,[r['reach_percent'] for r in r],width,label=label)
        ax.set(xticks=x,xticklabels=['10%','25%','50%'],xlabel='Assumed transmission probability',
               ylabel='Average percentage reached',ylim=(0,100));ax.legend(ncol=2,frameon=False)
        ax.spines[['top','right']].set_visible(False);fig.tight_layout()
        fig.savefig(out/f'{name}_reach.png',dpi=180);plt.close(fig)
    print(json.dumps(meta,indent=2))
    for r in summary:print(r['network'],r['probability'],r['strategy'],round(r['mean_reach'],4),round(r['reach_percent'],2))
    print('All model checks passed. Results saved in',out)

main()
with (ROOT / "results" / "simulation_summary.csv").open() as f:
    simulation_results = list(csv.DictReader(f))
for row in simulation_results:
    if row["network"] == "either_report":
        print(f"{float(row['probability']):.0%} {row['strategy']:<20} Mean reach: {float(row['mean_reach']):.4f} Reached: {float(row['reach_percent']):.2f}%")
display(Image(filename=str(ROOT / "results" / "either_report_reach.png")))
with (ROOT / "results" / "paired_comparisons.csv").open() as f:
    comparisons = list(csv.DictReader(f))
for network in ["either_report", "mutual_report"]:
    print("\n", network)
    for row in comparisons:
        if row["network"] == network:
            difference = Decimal(row["additional_reach_vs_random"]).quantize(Decimal("0.001"), rounding=ROUND_HALF_EVEN)
            print(f"{float(row['probability']):.0%} {row['strategy']:<20} Difference: {difference:+} Interval: [{float(row['paired_mc95_low']):+.3f}, {float(row['paired_mc95_high']):+.3f}]")
for row in simulation_results:
    if row["network"] == "mutual_report":
        print(f"{float(row['probability']):.0%} {row['strategy']:<20} Mean reach: {float(row['mean_reach']):.4f} Reached: {float(row['reach_percent']):.2f}%")
display(Image(filename=str(ROOT / "results" / "mutual_report_reach.png")))
