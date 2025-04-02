```cpp
#include <vector>
#include <algorithm>
#include <unordered_map>
using namespace std;

// Store all available beams
vector<int> beams;

// For each height, store all combinations of beams that can make it
struct Combination {
    vector<int> beamIndices;  // Indices of beams used
    int maxBeamLength;        // Length of the longest beam
};
unordered_map<int, vector<Combination>> heightToCombinations;

// For efficient lookup in requireTwin
unordered_map<int, int> heightToSingleResult;

void init() {
    beams.clear();
    heightToCombinations.clear();
    heightToSingleResult.clear();
}

void addBeam(int mLength) {
    int newBeamIdx = beams.size();
    beams.push_back(mLength);

    // Clear the cache for requireSingle since new combinations might change results
    heightToSingleResult.clear();

    // Case 1: Use just this beam
    Combination single;
    single.beamIndices = { newBeamIdx };
    single.maxBeamLength = mLength;
    heightToCombinations[mLength].push_back(single);

    // Case 2: Use this beam + one existing beam
    for (int i = 0; i < newBeamIdx; i++) {
        int height = mLength + beams[i];
        Combination pair;
        pair.beamIndices = { i, newBeamIdx };
        pair.maxBeamLength = max(mLength, beams[i]);
        heightToCombinations[height].push_back(pair);
    }

    // Case 3: Use this beam + two existing beams
    for (int i = 0; i < newBeamIdx; i++) {
        for (int j = i + 1; j < newBeamIdx; j++) {
            int height = mLength + beams[i] + beams[j];
            Combination triple;
            triple.beamIndices = { i, j, newBeamIdx };
            triple.maxBeamLength = max({ mLength, beams[i], beams[j] });
            heightToCombinations[height].push_back(triple);
        }
    }
}

int requireSingle(int mHeight) {
    // Check if result is cached
    auto cacheIt = heightToSingleResult.find(mHeight);
    if (cacheIt != heightToSingleResult.end()) {
        return cacheIt->second;
    }

    auto it = heightToCombinations.find(mHeight);
    if (it == heightToCombinations.end()) {
        heightToSingleResult[mHeight] = -1;
        return -1;  // Cannot create the column
    }

    // Find the minimum longest beam
    int minLongestBeam = 1e9;
    for (const auto& combo : it->second) {
        minLongestBeam = min(minLongestBeam, combo.maxBeamLength);
    }

    // Cache and return result
    heightToSingleResult[mHeight] = minLongestBeam;
    return minLongestBeam;
}

bool hasOverlap(const vector<int>& indices1, const vector<int>& indices2) {
    // Fast overlap check for small vectors
    for (int idx1 : indices1) {
        for (int idx2 : indices2) {
            if (idx1 == idx2) return true;
        }
    }
    return false;
}

int requireTwin(int mHeight) {
    auto it = heightToCombinations.find(mHeight);
    if (it == heightToCombinations.end()) {
        return -1;  // Cannot create the column
    }

    const auto& combinations = it->second;
    int n = combinations.size();

    if (n < 2) return -1;  // Need at least 2 combinations

    int minMaxBeamLength = 1e9;
    bool canCreate = false;

    // Pre-sort combinations by maxBeamLength to enable early breaks
    vector<const Combination*> sortedCombos;
    for (const auto& combo : combinations) {
        sortedCombos.push_back(&combo);
    }
    sort(sortedCombos.begin(), sortedCombos.end(),
        [](const Combination* a, const Combination* b) {
            return a->maxBeamLength < b->maxBeamLength;
        });

    // Try all pairs of combinations
    for (int i = 0; i < n; i++) {
        const auto& combo1 = *sortedCombos[i];

        // If this combo's max beam is already ≥ our best result, we can break early
        if (combo1.maxBeamLength >= minMaxBeamLength && canCreate) break;

        for (int j = i + 1; j < n; j++) {
            const auto& combo2 = *sortedCombos[j];

            // Maximum possible beam length for this pair
            int maxBeam = max(combo1.maxBeamLength, combo2.maxBeamLength);

            // If this pair can't improve our best result, skip it
            if (maxBeam >= minMaxBeamLength && canCreate) continue;

            // Check if the two combinations don't overlap
            if (!hasOverlap(combo1.beamIndices, combo2.beamIndices)) {
                canCreate = true;
                minMaxBeamLength = maxBeam;
            }
        }
    }

    return canCreate ? minMaxBeamLength : -1;
}
```
