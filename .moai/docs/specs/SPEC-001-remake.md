# SPEC-001: Uncharted Waters 2 Complete Remake

## Document Information

| Field | Value |
|-------|-------|
| SPEC ID | SPEC-001 |
| Title | Uncharted Waters 2 Complete Remake |
| Status | Draft |
| Created | 2025-01-02 |
| Author | MoAI-ADK |
| Priority | High |

---

## 1. Overview

### 1.1 Project Summary

This specification defines the complete remake of "Uncharted Waters 2: New Horizons" (대항해시대 2), originally released by KOEI in 1993. The remake will feature modern 2D illustration graphics while preserving the core gameplay experience with contemporary UI/UX improvements.

### 1.2 Project Goals

- Recreate the beloved sailing simulation experience for modern mobile platforms
- Modernize the user interface for touch-based interaction
- Create beautiful 2D illustrated graphics while respecting the original aesthetic
- Maintain game balance and depth that made the original a classic

### 1.3 Target Platform

- Primary: iOS and Android mobile devices
- Secondary: Desktop (Windows, macOS, Linux)

---

## 2. Requirements

### 2.1 Functional Requirements

#### FR-001: Character System
- WHEN the player starts a new game
- THE SYSTEM SHALL allow selection of one of six protagonists
- Each protagonist SHALL have a unique storyline and starting conditions

| Character | Nation | Career Focus |
|-----------|--------|--------------|
| Joao Ferrero | Portugal | Adventure/Exploration |
| Catalina Erantzo | Spain | Piracy/Combat |
| Otto Spinola | England | Naval Combat |
| Ernst Lopez | Netherlands | Trade/Commerce |
| Pietro Conti | Italy | Adventure/Art |
| Ali Vezas | Ottoman | Trade/Exploration |

#### FR-002: Navigation System
- WHEN the player is at sea
- THE SYSTEM SHALL simulate realistic wind and current effects on ship movement
- THE SYSTEM SHALL display a world map with 129 ports across Europe, Africa, Asia, and Americas
- THE SYSTEM SHALL calculate optimal routes based on wind patterns

#### FR-003: Trade System
- WHEN the player enters a port
- THE SYSTEM SHALL display available trade goods with dynamic pricing
- THE SYSTEM SHALL support 46 trade item types across 13 regions
- Price fluctuation SHALL be based on supply/demand and player actions

#### FR-004: Ship Management
- THE SYSTEM SHALL support 30+ ship types with unique specifications
- WHEN the player owns ships
- THE SYSTEM SHALL allow customization of weapons, sails, and cargo capacity
- Fleet management SHALL support up to 5 ships in the player's fleet

#### FR-005: Combat System
- WHEN hostile ships are encountered
- THE SYSTEM SHALL provide turn-based naval combat
- Combat options SHALL include cannon fire, boarding, and retreat
- THE SYSTEM SHALL calculate damage based on ship stats and crew condition

#### FR-006: Story and Events
- WHEN story conditions are met
- THE SYSTEM SHALL trigger narrative events and cutscenes
- THE SYSTEM SHALL track progress for each character's unique storyline
- Side quests SHALL be available independent of main story progression

### 2.2 Non-Functional Requirements

#### NFR-001: Performance
- THE SYSTEM SHALL maintain 60 FPS on devices released after 2020
- Initial load time SHALL be under 5 seconds
- Battery consumption SHALL be optimized for mobile play sessions

#### NFR-002: Usability
- THE SYSTEM SHALL support intuitive touch controls
- UI elements SHALL be appropriately sized for mobile screens (minimum 44pt touch targets)
- THE SYSTEM SHALL support both portrait and landscape orientations

#### NFR-003: Data Management
- Game progress SHALL be saved locally using device storage
- THE SYSTEM SHALL support multiple save slots (minimum 3)
- Save data export/import SHALL be available for backup purposes

#### NFR-004: Accessibility
- THE SYSTEM SHALL support font size adjustment
- Important information SHALL not rely solely on color differentiation
- THE SYSTEM SHALL provide visual feedback for all interactions

---

## 3. Technical Architecture

### 3.1 Technology Stack

| Component | Technology | Rationale |
|-----------|------------|-----------|
| Game Engine | Godot 4.x | Free, 2D optimized, lightweight builds |
| Language | GDScript | Rapid development, Python-like syntax |
| Data Format | JSON | Human-readable, easy to modify |
| Graphics | PNG/WebP | Wide compatibility, good compression |
| Audio | OGG Vorbis | Good compression, Godot native |
| Version Control | Git | Industry standard |

### 3.2 Project Structure

```
unchartedwaters2-remake/
├── project.godot
├── assets/
│   ├── characters/          # Character illustrations
│   ├── ships/               # Ship graphics
│   ├── backgrounds/         # Port and sea backgrounds
│   ├── ui/                  # UI elements and icons
│   ├── items/               # Trade goods and item icons
│   └── audio/               # Music and sound effects
├── data/
│   ├── ports.json           # Port database (129 ports)
│   ├── ships.json           # Ship specifications (30+ types)
│   ├── goods.json           # Trade goods (46 types)
│   ├── characters.json      # Character data
│   └── events.json          # Story event triggers
├── scenes/
│   ├── main_menu/
│   ├── world_map/
│   ├── port/
│   ├── sea_navigation/
│   ├── combat/
│   └── ui/
├── scripts/
│   ├── core/                # Core game logic
│   ├── systems/             # Game systems (trade, combat, etc.)
│   └── utils/               # Utility functions
└── docs/
    └── design/              # Design documents
```

### 3.3 Data-Driven Design

All game content SHALL be externalized to JSON files:

**ports.json example:**
```json
{
  "id": "lisbon",
  "name": { "en": "Lisbon", "ko": "리스본" },
  "region": "iberia",
  "nation": "portugal",
  "position": { "x": 145, "y": 320 },
  "facilities": ["harbor", "shipyard", "market", "tavern", "palace"],
  "specialties": ["wine", "olive_oil"]
}
```

**ships.json example:**
```json
{
  "id": "caravel",
  "name": { "en": "Caravel", "ko": "카라벨" },
  "type": "sailing",
  "capacity": 120,
  "durability": 80,
  "speed": 18,
  "maneuverability": 22,
  "crew_min": 10,
  "crew_max": 40
}
```

---

## 4. Art Direction

### 4.1 Visual Style

- **Style**: 2D Hand-drawn Illustration
- **Reference**: Visual novel style character portraits, painterly backgrounds
- **Color Palette**: Warm, atmospheric tones reminiscent of Renaissance art
- **Resolution**: 1920x1080 base, scalable for various devices

### 4.2 Asset Requirements

| Category | Count | Description |
|----------|-------|-------------|
| Character Portraits | ~230 | 6 protagonists × 10 expressions + 50 NPCs × 3 expressions |
| Ship Illustrations | 30 | One per ship type |
| Port Backgrounds | 15 | Grouped by region type |
| World Map | 1 | High-resolution scrollable map |
| UI Elements | 100+ | Buttons, frames, icons |
| Trade Item Icons | 46 | One per item type |

### 4.3 Placeholder Strategy

Development SHALL proceed with placeholder graphics:
- Phase 1: Geometric shapes and solid colors
- Phase 2: Basic sketches and wireframes
- Phase 3: Final illustrated assets

---

## 5. Development Phases

### Phase 1: Core Prototype (Target: Q2 2025)

**Scope:**
- Basic project setup in Godot 4
- World map navigation (scroll, zoom)
- Single protagonist (Joao)
- 5 ports functional (Lisbon, Seville, London, Amsterdam, Istanbul)
- Basic sailing mechanics
- Simple trade system (buy/sell)
- Placeholder graphics throughout

**Acceptance Criteria:**
- Player can sail between 5 ports
- Player can buy and sell goods
- Game state persists across sessions

### Phase 2: System Completion (Target: Q4 2025)

**Scope:**
- All 6 protagonists selectable
- 20+ ports functional
- Complete ship system (purchase, upgrade, fleet)
- Combat system implementation
- Weather and event system
- Basic story framework

**Acceptance Criteria:**
- All core gameplay systems functional
- 4+ hours of gameplay content
- Alpha testing with placeholder graphics

### Phase 3: Content Expansion (Target: Q2 2026)

**Scope:**
- All 129 ports implemented
- Complete trade good system
- Full story content for all 6 characters
- Side quests and discoveries
- Beta-quality graphics (50% final assets)

**Acceptance Criteria:**
- Complete gameplay from start to finish
- Beta testing program launched
- Performance optimized for target devices

### Phase 4: Polish and Release (Target: Q4 2026)

**Scope:**
- Final graphics and audio
- UI/UX refinement
- Localization (Korean, English, Japanese)
- Store preparation and submission
- Marketing materials

**Acceptance Criteria:**
- App store approval
- No critical bugs
- Positive beta tester feedback

---

## 6. Risk Assessment

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Solo developer burnout | Medium | High | Maintain playable state at all times; take regular breaks |
| Scope creep | High | Medium | Strict adherence to phase scope; defer non-essential features |
| Art asset production | High | High | Use AI tools for prototyping; consider outsourcing critical assets |
| Legal concerns (KOEI IP) | Low | High | Create original content inspired by, not copied from, original |
| Technical challenges | Medium | Medium | Leverage Godot community; prototype risky features early |

---

## 7. Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Core Loop Completion | Phase 1 | Player can complete trade route |
| Full Game Completion | Phase 4 | Player can finish one character story |
| Mobile Performance | 60 FPS | Automated testing on reference devices |
| Load Time | < 5 seconds | Cold start measurement |
| Session Length | 15+ minutes | Analytics (post-launch) |
| App Store Rating | 4.0+ | Store metrics (post-launch) |

---

## 8. Dependencies

### 8.1 External Dependencies

- Godot Engine 4.x (stable release)
- Mobile platform SDKs (iOS, Android)
- Art creation tools (Clip Studio, Procreate, or equivalent)
- Audio creation/licensing

### 8.2 Data Sources

- Original game data extraction (port locations, trade goods, ship specs)
- Fan wikis and FAQs for reference data
- Historical research for authenticity

---

## 9. Open Questions

1. **Legal Status**: Should we consult legal counsel regarding fan remake implications?
2. **Monetization**: Free with ads, paid, or freemium model?
3. **Multiplayer**: Consider online trading/competition features for future updates?
4. **Save Cloud Sync**: Implement cloud save for cross-device play?

---

## 10. Appendix

### A. Reference Materials

- [Namu Wiki - 대항해시대 2](https://namu.wiki/w/대항해시대%202)
- [Koei Wiki - Ships](https://koei.fandom.com/wiki/Uncharted_Waters:_New_Horizons/Ships)
- [GameFAQs - Market/Shipyard FAQ](https://gamefaqs.gamespot.com/snes/588823-new-horizons/faqs/62703)
- [Uncharted Waters Fan Site](https://unchartedwatersnewhorizons.com/)

### B. Original Game Asset Analysis

Located in current project:
- `/game/water2.zip` - Complete DOS game archive
- Contains: *.LZW (graphics), *.DAT (data), *.EXE (executables)
- 129 ports, 30+ ships, 46 trade goods confirmed

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1.0 | 2025-01-02 | MoAI-ADK | Initial draft |

