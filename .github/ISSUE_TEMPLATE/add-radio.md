---
name: Add Radio Station
about: Submit a new radio station to the directory
title: "[STATION] "
labels: station
assignees: ''
---

## Station Details

**Name:** 
**Stream URL:** 
**Genre:** (music/news/talk/sports/community/religious/other)
**Country:** 
**Website:** (optional)

## Verification
- [ ] I have verified this stream works
- [ ] This is a legal, public radio stream
- [ ] // Add this function to your script
async function loadStationsFromGitHub() {
    const repoOwner = 'YOUR_USERNAME'; // <-- CHANGE THIS
    const repoName = 'radio-directory'; // <-- CHANGE THIS
    
    try {
        const response = await fetch(`https://api.github.com/repos/${repoOwner}/${repoName}/issues?labels=station&state=open`);
        const issues = await response.json();
        
        const githubStations = issues.map(issue => {
            // Parse issue body to extract station details
            const body = issue.body;
            const name = body.match(/\*\*Name:\*\*\s*(.+)/)?.[1] || issue.title.replace('[STATION] ', '');
            const url = body.match(/\*\*Stream URL:\*\*\s*(.+)/)?.[1];
            const genre = body.match(/\*\*Genre:\*\*\s*(.+)/)?.[1] || 'other';
            const country = body.match(/\*\*Country:\*\*\s*(.+)/)?.[1] || '🌍 Global';
            
            if (!url) return null;
            
            return {
                id: issue.number + 10000,
                name: name,
                url: url,
                genre: genre,
                country: country,
                icon: getIconForGenre(genre),
                addedBy: issue.user.login,
                source: 'github'
            };
        }).filter(Boolean);
        
        // Merge with local stations
        const localStations = JSON.parse(localStorage.getItem('radioStations') || '[]');
        stations = [...githubStations, ...localStations];
        renderStations();
        
    } catch (error) {
        console.log('Could not load from GitHub, using local stations only');
    }
}

// Call this on init instead of just loading from localStorage
function init() {
    loadStationsFromGitHub();
}
