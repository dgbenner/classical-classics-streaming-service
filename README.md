# classical-classics-streaming-service

# ClassicalStream - Public Domain Classical Music Streaming Platform

A Progressive Web App (PWA) for streaming curated public domain classical music with integrated advertising monetization.

## Overview

ClassicalStream is a free-tier classical music streaming service that addresses the fundamental shortcomings of existing platforms while leveraging public domain recordings. The platform emphasizes proper metadata architecture, seamless multi-movement playback, and discovery tools specifically designed for classical music audiences.

## Core Value Proposition

### What Makes This Different

**For Listeners:**
- **Public domain focus**: Legally free, high-quality recordings from master performers
- **Intelligent metadata**: Search by composer, work, opus number, key, movement, performer, conductor, orchestra, and recording year
- **Gapless playback**: Multi-movement works play continuously without interruption
- **Context-rich experience**: Program notes, composer biographies, work histories, and performance notes
- **Discovery by era/style**: Browse by Baroque, Classical, Romantic, Modern periods with sub-categorization
- **Work-centric organization**: Find ALL recordings of a specific work, not just random "songs"

**For Advertisers:**
- **Captive, educated audience**: Classical listeners are statistically higher-income, better-educated demographics
- **Premium ad placement**: Audio ads between works (not mid-movement), visual display ads in UI
- **Contextual targeting**: Match ads to composer era, instrumentation, or mood
- **Engaged listening**: Average classical session length 45+ minutes vs 20-30 for pop

## Public Domain Music Sources

### Primary Content Repositories

1. **Musopen** (musopen.org)
   - 100,000+ recordings and sheet music
   - Organized by composer, period, instrument
   - CC0 and public domain
   - API available for integration

2. **IMSLP/Petrucci Music Library** (imslp.org)
   - 600,000+ scores, 80,000+ recordings
   - Comprehensive historical archive
   - Recordings from 1900-1960s entering public domain
   - Active community-contributed content

3. **Open Goldberg Variations & Similar Projects**
   - High-quality purpose-recorded public domain performances
   - Open Well-Tempered Clavier
   - Various Creative Commons initiatives

4. **Free Music Archive - Classical**
   - Curated classical selections
   - Royalty-free with clear licensing
   - Contemporary classical alongside traditional

5. **Classicals.de**
   - New recordings released as public domain
   - Growing library of freshly recorded works
   - High-quality MP3/FLAC formats

6. **Archive.org**
   - Historical recordings (pre-1923 US recordings)
   - Radio broadcasts from 1940s-1950s
   - Live concert archives

## Technical Architecture

### Frontend (Progressive Web App)

```
Technology Stack:
- Framework: React 18+ with TypeScript
- State Management: Redux Toolkit
- Audio Player: HTML5 Audio API with Media Session API
- Styling: Tailwind CSS
- PWA Features: Workbox for service workers
- Build Tool: Vite
```

**Key PWA Capabilities:**
- Offline playback with cached playlists
- Install to home screen (iOS, Android, Desktop)
- Background audio with lock screen controls
- Low data mode with adaptive bitrate
- Push notifications for new releases/playlists

### Backend Architecture

```
Microservices Structure:
1. Content Service (Node.js/Express)
   - Metadata management
   - Search/discovery API
   - Playlist generation

2. Audio Delivery Service
   - CDN integration (Cloudflare/BunnyCDN)
   - Adaptive streaming (HLS optional)
   - Analytics tracking

3. Ad Service
   - Google Ad Manager integration
   - Audio ad insertion
   - Campaign tracking
   
4. User Service
   - Listening history
   - Favorites/playlists
   - Preference management
```

### Data Storage

```
Primary Database: PostgreSQL
- Extensive metadata schema (see below)
- Full-text search capabilities
- Relational data for works/recordings/artists

Cache Layer: Redis
- Session data
- Frequently accessed metadata
- Playlist queues

Object Storage: AWS S3 / Cloudflare R2
- Audio files (MP3, FLAC)
- Album artwork
- Program notes PDFs

CDN: Cloudflare / BunnyCDN
- Global edge caching
- Reduced latency
- DDoS protection
```

### Metadata Schema

This is the critical differentiator for classical music:

```sql
-- Core tables solving classical music's metadata problem

TABLE composers (
  id UUID PRIMARY KEY,
  name VARCHAR(255),
  birth_year INT,
  death_year INT,
  era VARCHAR(50), -- Baroque, Classical, Romantic, Modern
  nationality VARCHAR(100),
  biography TEXT
)

TABLE works (
  id UUID PRIMARY KEY,
  composer_id UUID REFERENCES composers,
  title VARCHAR(500),
  subtitle VARCHAR(500),
  catalog_number VARCHAR(100), -- K. 331, Op. 67, BWV 1080
  key VARCHAR(50), -- "D Major", "C Minor"
  form VARCHAR(100), -- Symphony, Concerto, Sonata, etc.
  instrumentation TEXT[],
  composition_year INT,
  duration_minutes INT,
  movement_count INT
)

TABLE movements (
  id UUID PRIMARY KEY,
  work_id UUID REFERENCES works,
  movement_number INT,
  title VARCHAR(255),
  tempo_marking VARCHAR(100),
  key VARCHAR(50),
  duration_seconds INT
)

TABLE recordings (
  id UUID PRIMARY KEY,
  work_id UUID REFERENCES works,
  recording_date DATE,
  release_year INT,
  label VARCHAR(255),
  audio_quality VARCHAR(50), -- "MP3 320kbps", "FLAC"
  source_url TEXT,
  license VARCHAR(100) -- "Public Domain", "CC BY 4.0", etc.
)

TABLE recording_artists (
  recording_id UUID REFERENCES recordings,
  artist_id UUID REFERENCES artists,
  role VARCHAR(100) -- "Soloist", "Conductor", "Orchestra"
)

TABLE audio_files (
  id UUID PRIMARY KEY,
  recording_id UUID REFERENCES recordings,
  movement_id UUID REFERENCES movements,
  file_path TEXT,
  cdn_url TEXT,
  duration_seconds INT,
  file_size_bytes BIGINT,
  bitrate INT
)
```

## Monetization Strategy

### Advertising Model

**Free Tier (Ad-Supported):**
- Audio ads: 30-second spots every 3-4 works (~15-20 minutes)
- Visual ads: Banner in player interface (non-intrusive)
- Pre-roll: Optional 15-second ad before session start

**Ad Networks:**
- Google Ad Manager (primary)
- Programmatic audio: AdsWizz, AudioGo, AdTonos
- Direct sales for classical music sponsors (instrument makers, concert halls, luxury brands)

**Revenue Projections (Conservative):**
- CPM (Cost Per Mille): $15-25 for audio, $5-10 for display
- Average session: 45 minutes = 2-3 audio ads + continuous display
- 10,000 monthly active users × 10 sessions = 300,000 impressions
- Revenue: $4,500-7,500/month at scale

### Future Premium Tier (Optional)

- Ad-free experience: $4.99/month
- High-quality FLAC streaming
- Unlimited downloads for offline
- Exclusive playlists and content

## App Store Deployment

### Progressive Web App Approach

**Primary Distribution: Web-First**
- Responsive URL: classicalstream.app
- Install prompt after 2+ visits
- Works on ALL platforms without app stores

**Secondary: App Store Wrappers (Optional)**

**iOS App Store:**
- TWA (Trusted Web Activity) wrapper
- $99/year Apple Developer account
- Approval challenges: Apple's classical music competition
- Strategy: Emphasize public domain focus, educational value

**Google Play Store:**
- Free developer account ($25 one-time)
- TWA with Bubblewrap or PWABuilder
- Much easier approval than iOS
- Better PWA support

**Microsoft Store:**
- Free submission
- Excellent PWA support
- Desktop Windows market

### Hosting Recommendations

**Starting Phase (Free/Low-Cost):**
- Vercel/Netlify: Free PWA hosting
- Cloudflare Workers: API layer (free tier: 100k requests/day)
- Cloudflare R2: Storage ($0.015/GB)
- PostgreSQL: Supabase free tier or Railway

**Scaling Phase:**
- AWS/GCP: Full infrastructure
- Dedicated CDN: BunnyCDN ($0.01/GB)
- Database: Managed PostgreSQL (AWS RDS, GCP Cloud SQL)

## Development Roadmap

### Phase 1: MVP (Months 1-3)
- [ ] Metadata ingestion pipeline
- [ ] Core audio player with gapless playback
- [ ] Search by composer/work
- [ ] Basic playlist functionality
- [ ] PWA implementation
- [ ] 500+ works, 2,000+ recordings

### Phase 2: Enhanced Discovery (Months 4-6)
- [ ] Advanced filtering (era, instrumentation, conductor)
- [ ] Curated playlists (mood, occasion, period)
- [ ] Program notes integration
- [ ] User accounts and listening history
- [ ] Ad integration (Google Ad Manager)
- [ ] 2,000+ works, 8,000+ recordings

### Phase 3: Monetization & Scale (Months 7-12)
- [ ] Direct ad sales platform
- [ ] Analytics dashboard
- [ ] App store deployment (Android)
- [ ] Premium tier implementation
- [ ] 5,000+ works, 20,000+ recordings

### Phase 4: Community & Advanced Features (Year 2)
- [ ] User-submitted playlists
- [ ] Concert calendar integration
- [ ] Educational content partnerships
- [ ] iOS app store launch
- [ ] AI-powered recommendations
- [ ] Comprehensive catalog (10,000+ works)

## Addressing Classical Streaming Shortcomings

### Problem 1: Inadequate Metadata
**Industry Issue:** Pop music schemas (Artist/Album/Track) don't capture:
- Multiple performers (soloist, conductor, orchestra)
- Work vs. recording distinction
- Movement structure
- Catalog numbers (K. 331, BWV 1080)

**Our Solution:**
- Purpose-built classical schema with work-centric organization
- Search by ANY metadata field
- Complete attribution for all performers
- Multiple recordings per work easily comparable

### Problem 2: Fragmented Playback
**Industry Issue:** Multi-movement works split into tracks with gaps
- Beethoven's 9th Symphony: 4 separate "songs" with silence between
- Destroys listening experience

**Our Solution:**
- Gapless playback engine
- Movement-aware player (can skip movements without breaking flow)
- "Play complete work" as default action

### Problem 3: Poor Discovery
**Industry Issue:** Algorithmic recommendations favor pop listening patterns
- "Because you listened to Mozart" → suggests Smooth Jazz
- No understanding of period, style, instrumentation

**Our Solution:**
- Era-based navigation (Baroque → Vivaldi, Bach)
- Instrumentation filters (Piano Concertos, String Quartets)
- Form-based browsing (Symphonies, Sonatas, Operas)
- Musicologist-curated playlists, not algorithms

### Problem 4: Sound Quality
**Industry Issue:** Lossy compression (160-320 kbps) destroys dynamic range
- Classical's wide dynamics (pppp to ffff) become muddy
- Detail in orchestration lost

**Our Solution:**
- Minimum 320kbps MP3 for free tier
- FLAC lossless for premium tier
- Source files from high-quality public domain recordings

### Problem 5: Context Deficit
**Industry Issue:** No program notes, history, or listening guides
- Newcomers don't understand structure (sonata form, fugue)
- No historical context

**Our Solution:**
- Program notes for every work
- Composer biographies
- Movement descriptions (tempo, key, character)
- "First time listener" guides
- Links to scores (public domain)

### Problem 6: Catalog Gaps
**Industry Issue:** Focus on "greatest hits" and recent recordings
- Historical performances unavailable
- Regional composers overlooked
- Alternative interpretations missing

**Our Solution:**
- Systematic catalog building from public domain sources
- Multiple recordings per work
- Historical recordings (1900-1960s)
- Lesser-known composers represented

## User Experience Flow

### Discovery Path Example

```
User: "I want to listen to Beethoven's Moonlight Sonata"

Traditional Service:
1. Search "moonlight sonata"
2. Get 200+ results, poorly organized
3. First result is a rock cover
4. Find actual piece, click play
5. Plays only 1st movement
6. Awkward silence, then random next track

ClassicalStream:
1. Search "moonlight" OR "beethoven sonata 14"
2. Get work page: Piano Sonata No. 14, Op. 27 No. 2 "Moonlight"
3. See: composer bio, composition date (1801), key (C# minor)
4. View 12 available recordings with performers, years
5. Click "Play complete work" on preferred recording
6. All 3 movements play seamlessly
7. Read program notes during playback
8. Auto-suggest: Other late Beethoven sonatas
```

## Legal Considerations

### Public Domain Status (US-focused)

**Compositions:**
- Pre-1929: Public domain
- 1929-1977: Complex, check carefully

**Recordings:**
- Pre-1923: Public domain
- 1923-1972: Entering public domain gradually (95 years after publication)
- Post-1972: State law protection until 2067 minimum

**Strategy:**
- Start with clearly public domain (pre-1923)
- Use Creative Commons licensed new recordings
- Document licensing for every file
- Comply with attribution requirements

### International Considerations
- EU: Life + 70 years (compositions), 50 years (recordings)
- Geo-blocking may be necessary for some content
- Start US-focused, expand internationally later

## Competitive Analysis

### Existing Services

**General Streaming (Spotify, Apple Music, Tidal):**
- Strengths: Massive catalogs, high bitrates (Tidal)
- Weaknesses: Poor metadata, fragmented playback, discovery

**Classical Specialists (Idagio, Apple Classical, Primephonic):**
- Strengths: Better metadata, classical-focused
- Weaknesses: Subscription required, limited free options
- **Our Edge**: Free tier, public domain focus, PWA-first

**Niche (Musopen, IMSLP):**
- Strengths: Free, public domain
- Weaknesses: Poor UX, limited curation, no mobile apps
- **Our Edge**: Modern UX, PWA, advertising model enables free tier

## Success Metrics

### User Engagement
- Monthly Active Users (MAU)
- Average session duration (target: 40+ minutes)
- Repeat visits per month (target: 8+)
- Playlist creation rate

### Content Metrics
- Works in catalog
- Recordings per work (target: 3+ for popular works)
- Metadata completeness (target: 95%+)

### Business Metrics
- Ad revenue per user per month (ARPU)
- Ad fill rate (target: 85%+)
- Premium conversion rate (if implemented)

## Contributing

(For open source version)
- Metadata corrections via GitHub issues
- Recording submissions with licensing proof
- Translation efforts for international expansion

## License

- **Application Code**: MIT License
- **Content**: Public Domain + CC-licensed recordings (attributed)
- **Metadata**: CC BY-SA 4.0

## Links & Resources

- **Production**: [classicalstream.app]
- **GitHub**: [repository URL]
- **Documentation**: [docs URL]
- **API Docs**: [api-docs URL]

---

**Built with ♫ for classical music lovers**
