---
TOCTitle: Tasks Appendix
ContentId: 6DCA48F5-0566-4AEB-9C4C-CCBBA2945347
PageTitle: Tasks Appendix
DateApproved: 9/8/2016
MetaDescription: Additional info for using task runners in Visual Studio Code.
---

# Appendix

This is additional information for [tasks](/docs/editor/tasks.md).

## Schema for tasks.json

The following interfaces define the schema of the tasks.json file.

```typescript

interface TaskConfiguration extends BaseTaskConfiguration {

	/**
	 * The configuration's version number
	 */
	version: string;

	/**
	 * Windows specific task configuration
	 */
	windows?: BaseTaskConfiguration;

	/**
	 * Mac specific task configuration
	 */
	osx?: BaseTaskConfiguration;

	/**
	 * Linux specific task configuration
	 */
	linux?: BaseTaskConfiguration;
}

interface BaseTaskConfiguration {

	/**
	 * The command to be executed. Can be an external program or a shell
	 * command.
	 */
	command: string;

	/**
	 * Specifies whether the command is a shell command and therefore must
	 * be executed in a shell interpreter (e.g. cmd.exe, bash, ...).
	 *
	 * Defaults to false if omitted.
	 */
	isShellCommand?: boolean;

	/**
	 * Specifies whether a global command is watching the filesystem. A task.json
	 * file can either contain a global isWatching property or a tasks property
	 * but not both.
	 */
	isWatching?: boolean;
	
	/**
	 * The command options used when the command is executed. Can be omitted.
	 */
	options?: CommandOptions;

	/**
	 * The arguments passed to the command. Can be omitted.
	 */
	args?: string[];

	/**
	 * Controls whether the output view of the running tasks is brought to front or not.
	 *
	 * Valid values are:
	 *   "always": bring the output window always to front when a task is executed.
	 *   "silent": only bring it to front if no problem matcher is defined for the task executed.
	 *   "never": never bring the output window to front.
	 *
	 * If omitted "always" is used.
	 */
	showOutput?: string;

	/**
	 * If set to false the task name is added as an additional argument to the
	 * command when executed. If set to true the task name is suppressed. If
	 * omitted false is used.
	 */
	suppressTaskName?: boolean;

	/**
	 * Some commands require that the task argument is highlighted with a special
	 * prefix (e.g. /t: for msbuild). This property can be used to control such
	 * a prefix.
	 */
	taskSelector?:string;

	/**
	 * The problem matcher to be used if a global command is executed (e.g. no tasks
	 * are defined). A tasks.json file can either contain a global problemMatcher
	 * property or a tasks property but not both.
	 */
	problemMatcher?: string | ProblemMatcher | (string | ProblemMatcher)[];

	/**
	 * The configuration of the available tasks. A tasks.json file can either
	 * contain a global problemMatcher property or a tasks property but not both.
	 */
	tasks?: TaskDescription[];
}

/**
 * Options to be passed to the external program or shell
 */
export interface CommandOptions {

	/**
	 * The current working directory of the executed program or shell.
	 * If omitted Ticino's current workspace root is used.
	 */
	cwd?: string;

	/**
	 * The environment of the executed program or shell. If omitted
	 * the parent process' environment is used.
	 */
	env?: { [key:string]:string; };
}

/**
 * The description of a task.
 */
export interface TaskDescription {

	/**
	 * The task's name
	 */
	taskName: string;

	/**
	 * Additional arguments passed to the command when this task is
	 * executed.
	 */
	args?: string[];

	/**
	 * Whether this task maps to the default build command.
	 */
	isBuildCommand?:boolean;

	/**
	 * Whether this task maps to the default test command.
	 */
	isTestCommand?: boolean;

	/**
	 * Whether the executed command is kept alive and is watching the file system.
	 */
	isWatching?:boolean;
	
	/**
	 * Controls whether the output view of the running tasks is brought to front or not.
	 * See BaseTaskConfiguration#showOutput for details.
	 */
	showOutput?: string;

	/**
	 * See BaseTaskConfiguration#suppressTaskName for details.
	 */
	suppressTaskName?: boolean;

	/**
	 * The problem matcher(s) to use to capture problems in the tasks
	 * output.
	 */
	problemMatcher?: string | ProblemMatcher | (string | ProblemMatcher)[];
}

/**
 * A description of a problem matcher that detects problems
 * in build output.
 */
export interface ProblemMatcher {

	/**
	 * The name of a base problem matcher to use. If specified the
	 * base problem matcher will be used as a template and properties
	 * specified here will replace properties of the base problem
	 * matcher
	 */
	base?: string;

	/**
	 * The owner of the produced VS Code problem. This is typically
	 * the identifier of a VS Code language service if the problems are
	 * to be merged with the one produced by the language service
	 * or 'external'. Defaults to 'external' if omitted.
	 */
	owner?: string;

	/**
	 * The severity of the VS Code problem produced by this problem matcher.
	 *
	 * Valid values are:
	 *   "error": to produce errors.
	 *   "warning": to produce warnings.
	 *   "info": to produce infos.
	 *
	 * The value is used if a pattern doesn't specify a severity match group.
	 * Defaults to "error" if omitted.
	 */
	severity?: string;

	/**
	 * Defines how filename reported in a problem pattern
	 * should be read. Valid values are:
	 *  - "absolute": the filename is always treated absolute.
	 *  - "relative": the filename is always treated relative to
	 *    the current working directory. This is the default.
	 *  - ["relative", "path value"]: the filename is always
	 *    treated relative to the given path value.
	 */
	fileLocation?: string | string[];

	/**
	 * The name of a predefined problem pattern, the inline definintion
	 * of a problem pattern or an array of problem patterns to match
	 * problems spread over multiple lines.
	 */
	pattern?: string | ProblemPattern | ProblemPattern[];
}

export interface ProblemPattern {

	/**
	 * The regular expression to find a problem in the console output of an
	 * executed task.
	 */
	regexp: string;

	/**
	 * The match group index of the filename.
	 */
	file: number;

	/**
	 * The match group index of the problems's location. Valid location
	 * patterns are: (line), (line,column) and (startLine,startColumn,endLine,endColumn).
	 * If omitted the line and column properties are used.
	 */
	location?: number;

	/**
	 * The match group index of the problem's line in the source file.
	 * Can only be omitted if location is specified.
	 */
	line?: number;

	/**
	 * The match group index of the problem's column in the source file.
	 */
	column?: number;

	/**
	 * The match group index of the problem's end line in the source file.
	 *
	 * Defaults to undefined. No end line is captured.
	 */
	endLine?: number;

	/**
	 * The match group index of the problem's end column in the source file.
	 *
	 * Defaults to undefined. No end column is captured.
	 */
	endColumn?: number;

	/**
	 * The match group index of the problem's severity.
	 *
	 * Defaults to undefined. In this case the problem matcher's severity
	 * is used.
	 */
	severity?: number;

	/**
	 * The match group index of the problems's code.
	 *
	 * Defaults to undefined. No code is captured.
	 */
	code?: number;

	/**
	 * The match group index of the message. Defaults to 0.
	 */
	message: number;

	/**
	 * Specifies if the last pattern in a multi line problem matcher should
	 * loop as long as it does match a line consequently. Only valid on the
	 * last problem pattern in a multi line problem matcher.
	 */
	loop?: boolean;
}
```
