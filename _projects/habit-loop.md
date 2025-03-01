---
layout: project
title: Habit Loop
date: 2025-01-10
description: A modern habit tracking Android application built with Kotlin and Jetpack Compose that helps users create, monitor, and maintain daily habits with customizable reminders, streaks tracking, and data visualization.
github_link: https://github.com/yourusername/habit-loop
---

<p class="message">
  A modern habit tracking Android application built with Kotlin and Jetpack Compose that helps users create, monitor, and maintain daily habits with customizable reminders, streaks tracking, and data visualization.
</p>

## Overview

Habit Loop is a sleek, user-friendly Android application designed to help users build and maintain positive habits. With features like daily streak tracking, personalized reminders, and comprehensive progress visualization, Habit Loop makes habit formation intuitive and rewarding. The app combines modern Android development practices with thoughtful UX design to create a seamless habit tracking experience.

## Technical Implementation

### Core Architecture

- **Kotlin**: 100% Kotlin codebase with Coroutines for asynchronous operations
- **Jetpack Compose**: Modern declarative UI toolkit for responsive and dynamic interfaces
- **MVVM Architecture**: Clear separation of concerns with ViewModel, Repository, and UI layers
- **Room Database**: Efficient local data persistence with SQLite abstraction
- **Dependency Injection**: Hilt for clean, testable dependency management
- **Material Design 3**: Implementation of latest Material Design guidelines with dynamic theming

### Code Quality & Testing

- Unit tests with JUnit and Mockito for business logic
- UI tests with Compose testing framework
- Repository pattern for data access abstraction
- Clean Architecture principles for maintainable, scalable code
- Ktlint and Detekt for code style enforcement

## Key Features

- **Habit Creation**: Create custom habits or choose from pre-made templates
- **Streak Tracking**: Monitor continuous completion streaks with visual indicators
- **Daily Check-ins**: Simple interface for marking habits as complete
- **Customizable Reminders**: Set personalized notifications for habit completion
- **Progress Analytics**: Visualize habit adherence over time with intuitive charts
- **Theme Customization**: Toggle between light and dark themes with dynamic color support
- **Data Export**: Export habit data as JSON for backup or analysis
- **Offline Support**: Full functionality without internet connection
- **Widget Integration**: Home screen widgets for quick habit tracking

## Domain Model

The following entity relationships form the core of Habit Loop:

- **Habit** - Core entity representing a tracked habit with name, description, and frequency
- **Completion** - Records individual habit completion events
- **Streak** - Tracks consecutive completions for motivation
- **Reminder** - Manages notification settings for habit reminders
- **Category** - Organizes habits into logical groupings
- **User Preference** - Stores application settings and theme preferences

## MVVM Implementation with Jetpack Compose

Habit Loop implements the MVVM architecture pattern, using Jetpack Compose for the UI layer:

```kotlin
@HiltViewModel
class HabitDetailViewModel @Inject constructor(
    private val habitRepository: HabitRepository,
    private val streakCalculator: StreakCalculator,
    savedStateHandle: SavedStateHandle
) : ViewModel() {

    private val habitId: Long = checkNotNull(savedStateHandle["habitId"])

    private val _uiState = MutableStateFlow(HabitDetailUiState())
    val uiState: StateFlow<HabitDetailUiState> = _uiState.asStateFlow()

    init {
        viewModelScope.launch {
            habitRepository.getHabitWithCompletionsFlow(habitId).collect { habitWithCompletions ->
                val currentStreak = streakCalculator.calculateCurrentStreak(habitWithCompletions.completions)
                val longestStreak = streakCalculator.calculateLongestStreak(habitWithCompletions.completions)

                _uiState.update { state ->
                    state.copy(
                        habit = habitWithCompletions.habit,
                        completions = habitWithCompletions.completions,
                        currentStreak = currentStreak,
                        longestStreak = longestStreak,
                        completionRate = calculateCompletionRate(habitWithCompletions)
                    )
                }
            }
        }
    }

    fun toggleTodayCompletion() {
        viewModelScope.launch {
            val today = LocalDate.now()
            val hasCompletedToday = uiState.value.completions.any {
                it.date == today
            }

            if (hasCompletedToday) {
                habitRepository.deleteCompletionForDate(habitId, today)
            } else {
                habitRepository.addCompletion(
                    HabitCompletion(
                        habitId = habitId,
                        date = today,
                        timestamp = System.currentTimeMillis()
                    )
                )
            }
        }
    }

    // Additional methods for habit management...
}
```

## Room Database Schema for Habit Tracking

Habit Loop uses Room for efficient local data storage:

```kotlin
@Entity(tableName = "habits")
data class HabitEntity(
    @PrimaryKey(autoGenerate = true) val id: Long = 0,
    val name: String,
    val description: String,
    val categoryId: Long,
    val frequency: Int, // Days per week
    val reminderTime: Long?, // Epoch timestamp for reminder
    val createdAt: Long,
    val color: Int,
    val iconId: Int,
    val isArchived: Boolean = false
)

@Entity(
    tableName = "habit_completions",
    foreignKeys = [
        ForeignKey(
            entity = HabitEntity::class,
            parentColumns = ["id"],
            childColumns = ["habitId"],
            onDelete = ForeignKey.CASCADE
        )
    ],
    indices = [Index("habitId")]
)
data class HabitCompletionEntity(
    @PrimaryKey(autoGenerate = true) val id: Long = 0,
    val habitId: Long,
    val date: LocalDate,
    val timestamp: Long
)

@Dao
interface HabitDao {
    @Transaction
    @Query("SELECT * FROM habits WHERE isArchived = 0 ORDER BY name ASC")
    fun getActiveHabitsWithCompletionsFlow(): Flow<List<HabitWithCompletions>>

    @Query("SELECT * FROM habits WHERE id = :habitId")
    fun getHabitFlow(habitId: Long): Flow<HabitEntity>

    @Insert
    suspend fun insertHabit(habit: HabitEntity): Long

    @Update
    suspend fun updateHabit(habit: HabitEntity)

    @Query("UPDATE habits SET isArchived = 1 WHERE id = :habitId")
    suspend fun archiveHabit(habitId: Long)

    // Additional queries for habit management...
}
```

## Jetpack Compose UI Implementation

Habit Loop uses Jetpack Compose for a modern, responsive UI:

```kotlin
@Composable
fun HabitCard(
    habit: Habit,
    currentStreak: Int,
    onCheckClick: () -> Unit,
    onCardClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    val habitColor = Color(habit.color)

    Card(
        modifier = modifier
            .fillMaxWidth()
            .clickable { onCardClick() },
        elevation = CardDefaults.cardElevation(defaultElevation = 2.dp),
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.surface
        )
    ) {
        Row(
            modifier = Modifier
                .padding(16.dp)
                .fillMaxWidth(),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween
        ) {
            Row(verticalAlignment = Alignment.CenterVertically) {
                Box(
                    modifier = Modifier
                        .size(48.dp)
                        .background(
                            color = habitColor.copy(alpha = 0.2f),
                            shape = CircleShape
                        ),
                    contentAlignment = Alignment.Center
                ) {
                    Icon(
                        painter = painterResource(id = habit.iconResId),
                        contentDescription = null,
                        tint = habitColor,
                        modifier = Modifier.size(24.dp)
                    )
                }

                Spacer(modifier = Modifier.width(16.dp))

                Column {
                    Text(
                        text = habit.name,
                        style = MaterialTheme.typography.titleMedium
                    )
                    Spacer(modifier = Modifier.height(4.dp))
                    Row(verticalAlignment = Alignment.CenterVertically) {
                        Icon(
                            imageVector = Icons.Filled.LocalFire,
                            contentDescription = null,
                            tint = MaterialTheme.colorScheme.primary,
                            modifier = Modifier.size(16.dp)
                        )
                        Spacer(modifier = Modifier.width(4.dp))
                        Text(
                            text = "$currentStreak day streak",
                            style = MaterialTheme.typography.bodySmall,
                            color = MaterialTheme.colorScheme.onSurfaceVariant
                        )
                    }
                }
            }

            Checkbox(
                checked = habit.completedToday,
                onCheckedChange = { onCheckClick() },
                colors = CheckboxDefaults.colors(
                    checkedColor = habitColor
                )
            )
        }
    }
}
```

## Theme Toggle Implementation

The app supports seamless theme switching with Material 3 dynamic colors:

```kotlin
@Composable
fun HabitLoopTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }
        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }

    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        shapes = Shapes,
        content = content
    )
}
```

## Data Export Feature

Habit Loop enables users to export their habit data as JSON:

```kotlin
class HabitDataExporter @Inject constructor(
    private val habitRepository: HabitRepository,
    private val context: Context
) {
    suspend fun exportHabitsToJson(uri: Uri): Result<Unit> = withContext(Dispatchers.IO) {
        try {
            val habits = habitRepository.getAllHabitsWithCompletions()
            val habitDtos = habits.map { it.toHabitDto() }

            val exportData = ExportData(
                version = BuildConfig.VERSION_CODE,
                exportDate = System.currentTimeMillis(),
                habits = habitDtos
            )

            val json = Json {
                prettyPrint = true
                ignoreUnknownKeys = true
            }

            val jsonString = json.encodeToString(exportData)

            context.contentResolver.openOutputStream(uri)?.use { outputStream ->
                outputStream.write(jsonString.toByteArray())
            } ?: throw IOException("Could not open output stream")

            Result.success(Unit)
        } catch (e: Exception) {
            Result.failure(e)
        }
    }
}
```

## Why Habit Loop Matters

Building consistent habits is challenging, especially in today's distraction-filled world. Habit Loop addresses this challenge by:

- Making habit tracking frictionless with a clean, intuitive interface
- Providing motivational elements like streaks to encourage consistency
- Offering visual feedback on progress to reinforce positive behavior
- Creating a flexible system that works for various habit types and schedules
- Respecting user privacy with a fully offline, on-device solution

Habit Loop transforms the often difficult process of habit formation into an engaging, rewarding experience that helps users make lasting positive changes in their lives.

## Outcomes

Habit Loop has received positive feedback from early users, with particular praise for:

- Intuitive user interface that makes daily check-ins effortless
- Motivational streak system that encourages consistency
- Customization options that adapt to different habit-forming needs
- Privacy-focused approach without requiring online accounts
- Battery-efficient implementation with minimal resource usage

The app continues to evolve based on user feedback, with planned enhancements including habit challenges, social sharing options, and advanced analytics.

<div class="project-links">
  <a href="https://github.com/yourusername/habit-loop" class="github-link">View on GitHub</a>
  <a href="https://play.google.com/store/apps/details?id=com.yourusername.habitloop" class="playstore-link">Get on Google Play</a>
</div>
<div class="project-meta">
  <span class="tech-badge">Kotlin</span>
  <span class="tech-badge">Jetpack Compose</span>
  <span class="tech-badge">Room</span>
  <span class="tech-badge">MVVM</span>
  <span class="tech-badge">Material 3</span>
  <span class="tech-badge">Coroutines</span>
  <span class="date-badge">January 2025</span>
</div>
