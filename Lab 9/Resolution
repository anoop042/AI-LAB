def parse_clause(clause_str):
    return set(clause_str.split('v'))


def get_complement(literal):
    return literal[1:] if literal.startswith('~') else '~' + literal


def resolve(ci, cj):
    resolvents = set()
    for literal in ci:
        complement = get_complement(literal)
        if complement in cj:
            new_clause = (ci - {literal}) | (cj - {complement})
            resolvents.add(frozenset(new_clause))
    return resolvents


def resolution(kb_clauses, query):
    negated_query = get_complement(query)
    kb = [parse_clause(clause) for clause in kb_clauses] + [parse_clause(negated_query)]

    print("\n-----------------------------")
    print("KnowledgeBase - Resolution")
    print("-----------------------------")
    print(f"\nKnowledge Base Clauses: {kb_clauses}")
    print(f"Query: {query}")
    print(f"Negated Query Added: {negated_query}")
    print("\nResolution Steps:\n")

    new = set()

    while True:
        pairs = [(kb[i], kb[j]) for i in range(len(kb)) for j in range(i + 1, len(kb))]
        for (ci, cj) in pairs:
            resolvents = resolve(ci, cj)
            for resolvent in resolvents:
                print(f"Resolving {set(ci)} and {set(cj)} => {set(resolvent)}")
                if not resolvent:
                    print("\n Knowledge Base entails the query (empty clause derived).")
                    return True
                new.add(resolvent)

        if new.issubset(set(map(frozenset, kb))):
            print("\n Knowledge Base does NOT entail the query (no empty clause derived).")
            return False

        for clause in new:
            if clause not in kb:
                kb.append(clause)


print("KnowledgeBase - Resolution")
print("-----------------------------")
print("Enter clauses for the Knowledge Base.")
print("Use 'v' for OR between literals (e.g., '~qv~pvr'), and separate each clause with a space.\n")

kb_input = input("Enter clauses: ").split()
query_input = input("Enter the query: ")

resolution(kb_input, query_input)
